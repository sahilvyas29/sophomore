##### (Challenge-2): Sophomore

##  🚀 Build Your Own NFT System on the Kalp Blockchain


#### What is this challenge about?
This challenge is about developing a Non-Fungible Token (NFT) system using the KRC-721 standard on the Kalp blockchain. We will create a smart contract in Go that handles the lifecycle of NFTs, including minting, transferring, and burning tokens. The challenge simulates real-world scenarios where NFTs are used for digital art, collectibles, and other unique assets.


#### What will we learn?
**By participating in this challenge, we will:**

- Gain hands-on experience with Go programming language.
- Understand the KRC-721 standard for NFTs.
- Learn how to develop and deploy smart contracts on the Kalp blockchain.
- Enhance your skills in blockchain development and decentralized applications (dapps).
- Explore the concepts of tokenization and digital asset management.
- Improve your problem-solving and coding abilities in a competitive environment.

---

## Ready to Get Started?

#### Let's understand the KRC-721 first !

Imagine you’re playing your favorite online game. You’ve just unlocked an epic sword that’s one-of-a-kind. No one else in the game has this exact sword, and it’s got special stats that make it super powerful. Now, wouldn’t it be awesome if you could prove that this sword is truly yours, trade it with other players, or even sell it outside the game? That’s where KRC-721 comes in.

**KRC-721** is a set of rules (or a standard) on the Kalp blockchain that allows developers to create unique digital items, just like that epic sword. These digital items are called Non-Fungible Tokens (NFTs). Unlike regular game currency or items (which are usually identical and can be swapped 1:1), NFTs are unique. Each one has its own identity and characteristics, making them more like collectibles than regular in-game items.

![NFT](images/KRC-20-nft.png)

---

## Checkpoint 0: 📦 Installation

##### Before you begin, ensure you have the following:

- **Step 1. 🖥 [Download-Go](https://go.dev/doc/install)**  

>  **Note:** Ensure you have Go version `>=1.19` but `<1.20` in [Go.mod file](https://github.com/PrabalParihar/BuildThon/blob/main/go.mod#L3)

---

**To start project, follow these steps:**

<!-- TODO: update git repo url -->

1. Clone the repository:

   ```sh
   git clone https://github.com/PrabalParihar/buildthon.git
   ```

2. Navigate to the project directory:

   ```sh
   cd sophomore
   ```

3. Install the dependencies:
   ```sh
   go mod tidy
   ```

---

##### After Executing the above command the folder Structure shows up as below:

```sh

Folder sophomore
├──vendor
├──go.mod
├──go.sum
├──krc
├──main.go
└── krc.go  (Your NFT contract file)
```

---

 <!-- TODO Change title -->

## Checkpoint 1: 🏗 NFT Contract Walkthrough

Let's **Open the file `krc.go`** and go deep dive into NFT contract.

> 💡 Refer docs for more detailed info :- [Write the smart contract](https://docs.kalp.studio/Dev-documentation/Kalp-DLT/Smart-Contract-Write-Test-Deploy-Interact/Write-the-smart-contract/)

#### 1. Initializing the Token Contract

The **Initialize** method is the first step in setting up your Kalp-based ERC721 token contract. This method assigns a name and symbol to the token collection, ensuring that these identifiers are consistently used throughout the token's lifecycle.

```go
func (c *TokenERC721Contract) Initialize(ctx kalpsdk.TransactionContextInterface, name string, symbol string) (bool, error) {
    err := ctx.PutStateWithoutKYC(nameKey, []byte(name))
    if err != nil {
        return false, fmt.Errorf("failed to set token name: %v", err)
    }
    err = ctx.PutStateWithoutKYC(symbolKey, []byte(symbol))
    if err != nil {
        return false, fmt.Errorf("failed to set token symbol: %v", err)
    }
    return true, nil
}
```

- **Parameters**:

  - `name (string)`: The human-readable name that designates the token collection, often aligning with its intended use cases or branding.

  - `symbol (string)`: A concise and recognizable symbol that serves as a shorthand identifier for the token collection, typically following conventional naming conventions (e.g., "MTK" or "KALP").

- **Return values:(bool, error)**: A boolean indicating success or failure, and an error object if any issues arise

#### 2. 🖨 Minting NFTs

The **MintWithTokenURI** method allows you to create new NFTs in the Kalp ecosystem. This method is crucial for establishing ownership and linking the NFT to its metadata.

```go
func (c *TokenERC721Contract) MintWithTokenURI(ctx kalpsdk.TransactionContextInterface, tokenId string, tokenURI string) (*Nft, error) {
    nft := &Nft{TokenId: tokenId, Owner: /* Owner's identity */, TokenURI: tokenURI}
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    nftBytes, _ := json.Marshal(nft)
    ctx.PutStateWithoutKYC(nftKey, nftBytes)
    return nft, nil
}
```

- **Parameters**:

  - `tokenId (string)`: The unique identifier for the NFT.

  - `tokenURI (string)`: A URI pointing to a JSON file with the NFT's metadata.

    _Example Json Format to store in uri_

    ```json
    {
    "name": "CryptoArt #1234",
    "description": "A unique piece of digital art from the CryptoArt collection.",
    "image": "https://example.com/images/cryptoart-1234.png",
    "attributes": [
        {
        "trait_type": "Background",
        "value": "Blue"
        },
        {
        "trait_type": "Eyes",
        "value": "Green"
        },
        {
        "trait_type": "Mouth",
        "value": "Smile"
        },
        {
        "trait_type": "Accessory",
        "value": "Sunglasses"
        }
    ],
    "external_url": "https://example.com/cryptoart/1234"
    }
    ```

- **Return values: (\*Nft, error)**: A pointer to the newly created NFT object and an error object if any issues occur.



#### 3. 🔥 Burning NFTs

The **Burn** method is used to permanently remove an NFT from the Kalp ecosystem, effectively destroying the token and revoking its ownership.

```go
func (c *TokenERC721Contract) Burn(ctx kalpsdk.TransactionContextInterface, tokenId string) (bool, error) {
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    ctx.DelStateWithoutKYC(nftKey)
    return true, nil
}
```

- **Parameters**:

    - `tokenId (string)`: The unique identifier that pinpoints the specific NFT destined for elimination.

- **Return values:(bool, error)**: The method indicates its success or failure by returning a boolean value, accompanied by an error object if any issues arise during the burning process.

#### 4. 🔄 Transferring NFTs
The **TransferFrom** method facilitates the transfer of an NFT from one owner to another within the Kalp ecosystem, ensuring a secure and verified transaction.

```go
func (c *TokenERC721Contract) TransferFrom(ctx kalpsdk.TransactionContextInterface, from string, to string, tokenId string) (bool, error) {
    nft, _ := _readNFT(ctx, tokenId)
    nft.Owner = to
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    nftBytes, _ := json.Marshal(nft)
    ctx.PutStateWithoutKYC(nftKey, nftBytes)
    return true, nil
}
```

- **Parameters**:
    - `from (string)`: The current owner of the NFT, relinquishing their control.

    - `to (string)`: The designated recipient who will assume ownership of the NFT.

    - `tokenId (string)`: The unique identifier that unequivocally designates the specific NFT being transferred.

- **Return Values:(bool, error)**: The method signals its success or failure through a boolean value, also returning an error object if any issues arise during the transfer process.

#### 5. ✏️ Approval & Authorization

##### a). Approve

The Approve method allows an NFT owner to grant another account the authority to transfer a specific NFT on their behalf.

```go
func (c *TokenERC721Contract) Approve(ctx kalpsdk.TransactionContextInterface, operator string, tokenId string) (bool, error) {
    nft, _ := _readNFT(ctx, tokenId)
    nft.Approved = operator
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    nftBytes, _ := json.Marshal(nft)
    ctx.PutStateWithoutKYC(nftKey, nftBytes)
    return true, nil
}
```

- **Parameters**:

    - `operator (string)`: The account designated to receive transfer approval for the specified NFT.

    - `tokenId (string)`: The unique identifier that pinpoints the NFT for which authorization is being granted.

- **Return Values:(bool, error)**: The method indicates success or failure through a boolean value, accompanied by an error object if any issues arise during the approval process.

##### b). SetApprovalForAll

The **SetApprovalForAll** method enables an NFT owner to grant an account the ability to manage all of their NFTs, making it easier to handle large collections.

```go
func (c *TokenERC721Contract) SetApprovalForAll(ctx kalpsdk.TransactionContextInterface, operator string, approved bool) (bool, error) {
    sender := /* Fetch sender's identity */
    approvalKey, _ := ctx.CreateCompositeKey(approvalPrefix, []string{sender, operator})
    approval := Approval{Owner: sender, Operator: operator, Approved: approved}
    approvalBytes, _ := json.Marshal(approval)
    ctx.PutStateWithoutKYC(approvalKey, approvalBytes)
    return true, nil
}
```

- **Parameters**:

    - `operator (string)`: The account designated to receive approval for managing all the caller's NFTs.

    - `approved (bool)`: A flag indicating whether to grant (true) or revoke (false) approval for the operator.

- **Return values:(bool, error)**: The method signals success or failure through a boolean value, along with an error object if any issues arise during the approval process.


#### 7.📊 Reading Functions

##### a). BalanceOf

The **BalanceOf** method provides the number of NFTs owned by a specific account, helping you track ownership.

```go
func (c *TokenERC721Contract) BalanceOf(ctx kalpsdk.TransactionContextInterface, owner string) int {
    iterator, _ := ctx.GetStateByPartialCompositeKey(balancePrefix, []string{owner})
    balance := 0
    for iterator.HasNext() {
        iterator.Next()
        balance++
    }
    return balance
}
```

- **Parameters**:

    - `owner (string)`: The account whose NFT holdings are being queried.
- **Return value:(int)**: An integer representing the total number of NFTs owned by the account.

##### b). OwnerOf

The **OwnerOf** method returns the current owner of a specific NFT, confirming ownership.

```go
func (c *TokenERC721Contract) OwnerOf(ctx kalpsdk.TransactionContextInterface, tokenId string) (string, error) {
    nft, err := _readNFT(ctx, tokenId)
    if err != nil {
        return "", err
    }
    return nft.Owner, nil
}

```

- **Parameters**:

    - `tokenId (string)`: The unique identifier of the NFT.
- **Return Values**:The owner’s account as a string, and an error object if any issues occur.


#### 7. 🏗 Read Contract Metadata 
##### a). Name

The *Name* method retrieves the name of the token collection, aiding in its identification and reference.

```go
func (c *TokenERC721Contract) Name(ctx kalpsdk.TransactionContextInterface) (string, error) {
    bytes, err := ctx.GetState(nameKey)
    if err != nil {
        return "", err
    }
    return string(bytes), nil
}
```

- **Return values**: The name of the token collection as a string, and an error object if any issues occur.

##### b). Symbol

The **Symbol** method fetches the symbol of the token collection, which serves as a concise identifier.

```go
func (c *TokenERC721Contract) Symbol(ctx kalpsdk.TransactionContextInterface) (string, error) {
    bytes, err := ctx.GetState(symbolKey)
    if err != nil {
        return "", err
    }
    return string(bytes), nil
}
```

- **Return values**: The symbol as a string, and an error object if any issues occur.


---

## Checkpoint 2: 📀 Deploy Smart Contract

Now, its time to deploy the contract.

Before you begin, ensure you have the account in [Kalp Studio Platform](https://console.kalp.studio), You can create the Kalp Studio Platform account by following the below docs

1. [Sign Up and Log In to Kalp Studio Platform](https://doc.kalp.studio/Getting-started/Onboarding/How-to-Sign-Up-and-Log-In-to-Kalp-Studio-Platform/)

Now, after having the account in Kalp Studio Platform. we can deploy the contract using Kalp Studio.

> 💡 Make sure you are connected to [Kalp Studio Wallet (guide)](https://docs.kalp.studio/Getting-started/Kalp-studio-wallet/How-to-Navigate-To-kalp-studio-Wallet/)

2. [Deploy a Smart Contract on KALP Studio (detail guide)](https://doc.kalp.studio/Dev-documentation/Kalp-DLT/Smart-Contract-Write-Test-Deploy-Interact/Deploy-the-smart-contract/)

    - **Step 1:** Access the Kalp Studio
Dashboard
    - **Step 2:** Go to Kalp Instant Deployer
    - **Step 3:** Click on "Create New" Smart Contract
    - **Step 4:** Enter the details
        - **Name:** Enter a name for your smart contract.
        - **Category:** Choose a category for your smart contract.
        - **Description:** Optionally, you can add a description for your smart contract.
    - **Step 4:** Upload your 'sophomore.zip' file.

> 💡 For more descriptive deployment guide, please refer to [detailed docs](https://doc.kalp.studio/Dev-documentation/Kalp-DLT/Smart-Contract-Write-Test-Deploy-Interact/Deploy-the-smart-contract/)

---

## Checkpoint 3: 🕹️ Interacte with smart contract

1. initialise contract
2. token minting
3. read the name of nft
4. burn

Before you interact with apis , ensure you have the following:

1. [Download Postman](https://www.postman.com/downloads/)

2. [Interacting with smart contract](https://doc.kalp.studio/Dev-documentation/Kalp-DLT/Smart-Contract-Write-Test-Deploy-Interact/Interacting-with-smart-contract/)

---

## Example To: 🔒 Interacte with smart contract With Postman

Before you begin, ensure you have the following:

1. **After Executing the above Setup**
2. **[Sign Up and Log In to Kalp Studio Platform](https://doc.kalp.studio/Getting-started/Onboarding/How-to-Sign-Up-and-Log-In-to-Kalp-Studio-Platform/)**
3. **[Deploy a Smart Contract on KALP Studio](https://doc.kalp.studio/Dev-documentation/Kalp-DLT/Smart-Contract-Write-Test-Deploy-Interact/Deploy-the-smart-contract/)**
4. **[Download Postman](https://www.postman.com/downloads/)**

### Example

---

After deploying the smart contract in Kalp Studio, an API endpoint will be generated. This API endpoint can be used to interact with the deployed smart contract.

**Here is an example of generated api end point Route look like this in Kalp studio**

![alt text](images/image.png)

---

**After that Click on **Check Params** , the routing details and parameters should Look Like this.**

![alt text](images/image-1.png)

---

- The code snippet requires an API key for authorization in an API post request.
- The API key can be generated using an API key generation process.

**That would look like this in Kalp Studio**

![alt text](images/image-2.png)

---

**Follow the process for api auth key generation**

![alt text](images/image-11.png)

---

#### This code snippet demonstrates how to use the `initialize` function and make a POST request using Postman.

#### Instructions

1. Copy the route of the `initialize` function and paste it into the URL box in Postman.

2. Copy the parameters **(Check Params)** of the `initialize` function and paste them into the body of Postman, selecting the raw json type.
3. Pass the custom value for the name and symbol of the NFT in the parameters. See the example **screen shot** on line number 6 and 7

#### Parameters

- `name`: The custom value for the name of the NFT.
- `symbol`: The custom value for the symbol of the NFT.

Note: Make sure to replace any placeholders or variables in the copied code with actual values before making the request.

![alt text](images/image-4.png)

---

#### Select header in postman

<!-- write ``` x-api ``` in key part and paste the auth key in value you got after apikeygeneration side bar in kalp studio -->

This code snippet demonstrates how to select a header in Postman and set the `x-api` key with the value obtained from the API key generation sidebar in Kalp Studio.

## Steps

1. In the request headers section.
2. Set the key as `x-api`.
3. Paste the auth key obtained from the API key generation sidebar in Kalp Studio as the value for the `x-api` header.

Note: Make sure you have the necessary permissions and access to the API key generation sidebar in Kalp Studio.

![alt text](images/image-5.png)

---

### Select the request method POST in postman

<!-- hit send request by using send button on postman -->

This code snippet provides instructions on how to select the request method POST in Postman and send a request using the send button.

#### Instructions

1. Select the request method as POST.
2. Click on the send button to send the request.

## Example

After follwing above instruction you will get some response in below response body

- `success`: A boolean value indicating the success of the initialization.
- `message`: A string containing the success message.
- `transactionid`: The ID of the transaction.

![alt text](images/image-7.png)

---

#### This code snippet demonstrates how to use the tokenURI function and make a GET request using Postman.

##### Instructions

- Copy the route of the tokenURI function and paste it into the URL box in Postman.

- Copy the parameters (Check Params) of the tokenURI function and paste them into the URL parameters section in Postman.

- Pass the custom value for the token ID in the parameters. See the example screen shot on line number 6.

###### Parameters

- tokenId: The custom value for the ID of the NFT.

Note: Make sure to replace any placeholders or variables in the copied code with actual values before making the request.

![alt text](images/image-8.png)

---

#### This code snippet demonstrates how to use the ownerOf function and make a GET request using Postman.

##### Instructions

- Copy the route of the ownerOf function and paste it into the URL box in Postman.

- Copy the parameters (Check Params) of the ownerOf function and paste them into the URL parameters section in Postman.
- Pass the custom value for the token ID in the parameters. See the example screen shot on line number 6.

##### Parameters

- tokenId: The custom value for the ID of the NFT.

Note: Make sure to replace any placeholders or variables in the copied code with actual values before making the request.

![alt text](images/image-9.png)

---

This README serves as an overview for developing and deploying KRC721 NFTs using Go on the Kalp blockchain. For additional details, refer to the Kalp SDK documentation.
Step by Step to Deploy a Smart Contract on KALP Studio
