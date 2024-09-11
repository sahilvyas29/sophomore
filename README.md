
---
# Shophomore : Challenge 2
<!-- TODO Add Flashy (Show-off) Title-->
<!-- 🚀 BuildThon: Mastering NFTs with Go -->
#  🚀 BuildThon: Mastering NFTs with Go

## Description
After deploying the smart contract in Kalp Studio, an API endpoint will be generated. This API endpoint can be used for further interactions with the smart contract.

## Usage
The generated API endpoint can be used to perform various operations on the deployed smart contract.

## Example
<!-- TODO Add good description-->
<!-- TODO What is this chalenge is about?-->
<!-- TODO What will I learn?-->
<!-- TODO What is the goal of this challenge? with executive summary-->

BuildThon is a Go-based KRC-721 NFT smart contract that allows the creation, management, and transfer of unique digital assets (NFTs). Each token represents a unique item and is stored on the blockchain with a one-to-one relationship between the token ID and its metadata.

#### What is this challenge about?
This challenge is about developing a Non-Fungible Token (NFT) system using the KRC-721 standard on the Kalp blockchain. Participants will create a smart contract in Go that handles the lifecycle of NFTs, including minting, transferring, and burning tokens. The challenge simulates real-world scenarios where NFTs are used for digital art, collectibles, and other unique assets.

#### What will You learn?
**By participating in this challenge, you will:**

- Gain hands-on experience with Go programming language.
- Understand the KRC-721 standard for NFTs.
- Learn how to develop and deploy smart contracts on the Kalp blockchain.
- Enhance your skills in blockchain development and decentralized applications (dapps).
- Explore the concepts of tokenization and digital asset management.
- Improve your problem-solving and coding abilities in a competitive environment.

### What is the goal of this challenge?
The goal of this challenge is to develop a fully functional NFT system that adheres to the KRC-721 standard.
Participants will create a smart contract that can mint,  transfer, and burn NFTs, as well as manage their metadata.
 - **The challenge aims to provide a comprehensive learning experience in kalp blockchain development.**

#### Executive Summary

The BuildThon Challenge is an intensive coding competition focused on developing a KRC-721 NFT smart contract using Go. Participants will gain valuable experience in blockchain development, smart contract programming, and decentralized applications. The challenge offers a unique opportunity to learn, innovate, and showcase your skills in the rapidly evolving field of blockchain technology. Join us and take your coding skills to the next level!

## What is KRC-721?

The KRC-721 introduces a standard for NFT, in other words, this type of Token is unique and can have different value than another Token from the same Smart Contract, maybe due to its age, rarity or even something else like its visual. Wait, visual?

Yes! All NFTs have a uint256 variable called tokenId, so for any KRC-721 Contract, the pair contract address, uint256 tokenId must be globally unique. That said, a dapp can have a "converter" that uses the tokenId as input and outputs an image of something cool, like zombies, weapons, skills or amazing kitties!


---

## Checkpoint 0: 📦 Installation

### Before you begin, ensure you have the following:

- **Step 1. 🖥 [Download-Go](https://go.dev/doc/install) Go version `>=1.19` but `<1.20`.**

---

**To start project, follow these steps:**
<!-- TODO: update git repo url -->
1. Clone the repository:
   ```sh
   git clone https://github.com/yourusername/buildthon.git
   ```

2. Navigate to the project directory:
   ```sh
   cd buildthon
   ```

3. Install the dependencies:
   ```sh  
   go mod tidy
   ```

---

<!-- TODO: add folder name -->
### Folder Structure: After Executing the above command the folder Structure shows up as below:
 ```sh

Folder buildthon
├──vendor
├──go.mod
├──go.sum
├──krc
├──main.go
└── krc.go  (Your NFT contract file)
```
---
 <!-- TODO Change title -->
## Checkpoint 1: 🏗 KRC-NFT Contract Functions Walkthrough


**Open the file "KRC.go".**

#### 1. Initialize Token Contract
The [Initialize method](https://github.com/PrabalParihar/BuildThon/blob/main/krc.go#L524) serves as a crucial first step in configuring a Kalp-based ERC721 token contract. It establishes the name and symbol that will be indelibly associated with the token collection, ensuring clarity and consistency throughout its lifecycle.

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
##### Parameters:

- **name (string)**: The human-readable name that designates the token collection, often aligning with its intended use cases or branding.

- **symbol (string)**: A concise and recognizable symbol that serves as a shorthand identifier for the token collection, typically following conventional naming conventions (e.g., "MTK" or "KALP").

**Return Values:(bool, error)**: The method returns a boolean value indicating success or failure, accompanied by an error object if any issues arise during initialization.



---

### Checkpoint 2: 🖨 Minting NFTs Function Walkthrough
The MintWithTokenURI method holds the power to create brand-new NFTs (Non-Fungible Tokens) within the Kalp ecosystem, cementing their unique existence and linking them with rich metadata. It serves as a cornerstone for establishing ownership and unlocking the diverse possibilities that NFTs offer.`MintWithTokenURI`.

```go
func (c *TokenERC721Contract) MintWithTokenURI(ctx kalpsdk.TransactionContextInterface, tokenId string, tokenURI string) (*Nft, error) {
    nft := &Nft{TokenId: tokenId, Owner: /* Owner's identity */, TokenURI: tokenURI}
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    nftBytes, _ := json.Marshal(nft)
    ctx.PutStateWithoutKYC(nftKey, nftBytes)
    return nft, nil
}
```
#### Parameters:

- **tokenId (string)**: An unequivocally unique identifier assigned to the NFT, ensuring its individuality and preventing any chance of duplication.

- **tokenURI (string)**: A Universal Resource Identifier (URI), acting as a digital address that points to a JSON file containing comprehensive metadata about the NFT's attributes, details, and potential functionalities.

**Return Values: (*Nft, error)**: Upon successful minting, the method returns a pointer to the newly created Nft object, containing its essential information. It also includes an error object if any issues arise during the process.


# Example Json Format to store in uri

Here is an example of a JSON format that can be used to store metadata for an NFT (Non-Fungible Token) in a URI:
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



---

### Checkpoint 3: 🔥 Burning NFTs Function Walkthrough
The Burn method wields the definitive power to permanently remove NFTs (Non-Fungible Tokens) from the Kalp ecosystem, executing an irreversible act of destruction that revokes their presence and ownership. It serves as a mechanism for managing token supply, addressing specific use cases, or even symbolizing artistic expression `Burn`.

```go
func (c *TokenERC721Contract) Burn(ctx kalpsdk.TransactionContextInterface, tokenId string) (bool, error) {
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    ctx.DelStateWithoutKYC(nftKey)
    return true, nil
}
```

#### Parameters:

- **tokenId (string)**: The unique identifier that pinpoints the specific NFT destined for elimination.

**Return Values:(bool, error)**: The method indicates its success or failure by returning a boolean value, accompanied by an error object if any issues arise during the burning process.



---

## Checkpoint 4: 🔄 Transferring NFTs Function Walkthrough
The TransferFrom method sits at the heart of NFT (Non-Fungible Token) movement within the Kalp ecosystem, meticulously orchestrating the transfer of ownership from one entity to another. It stands as the cornerstone for enabling seamless transactions, trading, and sharing of NFTs.`TransferFrom`.

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
#### Parameters:

- **from (string)**: The current owner of the NFT, relinquishing their control.

- **to (string)**: The designated recipient who will assume ownership of the NFT.

- **tokenId (string)**: The unique identifier that unequivocally designates the specific NFT being transferred.

**Return Values:(bool, error)**: The method signals its success or failure through a boolean value, also returning an error object if any issues arise during the transfer process.

---

## Checkpoint 5: ✏️ Approval & Authorization Function Walkthrough

### Approve
The Approve method empowers NFT (Non-Fungible Token) owners within the Kalp ecosystem to selectively bestow transfer authority upon designated accounts, granting them the ability to move specific NFTs on behalf of the owner. This flexibility facilitates a range of collaborative and trust-based interactions.

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
#### Parameters:

- **operator (string)**: The account designated to receive transfer approval for the specified NFT.

- **tokenId (string)**: The unique identifier that pinpoints the NFT for which authorization is being granted.

**Return Values:(bool, error)**: The method indicates success or failure through a boolean value, accompanied by an error object if any issues arise during the approval process.

### SetApprovalForAll Function Walkthrough
The SetApprovalForAll method within the Kalp ecosystem empowers NFT (Non-Fungible Token) owners to confer blanket approval to designated accounts (operators). This grants the operator the ability to transfer all of the owner's NFTs, acting as a powerful tool for managing large collections or streamlining specific use cases.

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
#### Parameters:

- **operator (string)**: The account designated to receive approval for managing all the caller's NFTs.

- **approved (bool)**: A flag indicating whether to grant (true) or revoke (false) approval for the operator.

**Return Values:(bool, error)**: The method signals success or failure through a boolean value, along with an error object if any issues arise during the approval process.

---

## Checkpoint 6: 📊 Read Functions Walkthrough

### BalanceOf
The BalanceOf method acts as a digital accountant within the Kalp ecosystem, meticulously cataloging NFT (Non-Fungible Token) collections. It enables you to precisely determine the number of NFTs held by a specific account, serving as a crucial tool for understanding ownership patterns, token scarcity, and personal collections.

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
#### Parameters:

- **owner (string)**: The account whose NFT holdings you wish to inspect.
**Return Value:int**: A concise integer representing the exact quantity of NFTs owned by the specified account.

### OwnerOf
Within the Kalp NFT landscape, the OwnerOf method serves as a steadfast guide, revealing the rightful owner of a specified NFT. It acts as a fundamental tool for establishing ownership, ensuring responsible transfer, and upholding accountability within this dynamic ecosystem.

```go
func (c *TokenERC721Contract) OwnerOf(ctx kalpsdk.TransactionContextInterface, tokenId string) (string, error) {
    nft, err := _readNFT(ctx, tokenId)
    if err != nil {
        return "", err
    }
    return nft.Owner, nil
}

```

#### Parameters:

- **tokenId (string)**: The unique identifier that singles out the NFT for which ownership information is sought.
Return Values:

- **Owner Account**: A string representing the account holding ownership of the inquired NFT.

Error: In the event of unforeseen retrieval obstacles, an error object is returned, shedding light on the nature of the difficulty.

---

## Checkpoint 7: 🏗 Read Contract Metadata Function Walkthrough

### Name
The Name method fulfills the role of a skillful archivist, divulging the distinct title bestowed upon a token collection. It serves as a fundamental means of identifying and referencing collections, fostering clarity and discoverability.

```go
func (c *TokenERC721Contract) Name(ctx kalpsdk.TransactionContextInterface) (string, error) {
    bytes, err := ctx.GetState(nameKey)
    if err != nil {
        return "", err
    }
    return string(bytes), nil
}
```
##### Return Values:

- **string**: The collection's carefully chosen name, representing its unique identity.

- **error**: In the unlikely event of retrieval obstacles, an error object surfaces to illuminate the nature of the difficulty.

### Symbol
The Symbol method acts as a herald, disclosing the succinct and potent symbol that serves as a collection's emblematic signature. It empowers concise identification and efficient referencing, akin to a heraldic crest in the digital realm.

```go
func (c *TokenERC721Contract) Symbol(ctx kalpsdk.TransactionContextInterface) (string, error) {
    bytes, err := ctx.GetState(symbolKey)
    if err != nil {
        return "", err
    }
    return string(bytes), nil
}
```
##### Return Values:

- **string**: The collection's meticulously chosen symbol, acting as its concise identifier and resonant emblem.

- **error**: Should retrieval obstacles arise, an error object emerges to shed light on the nature of the difficulty.

---

## Checkpoint 8: 🔒 Deploy Smart Contract

Before you begin, ensure you have the account in Kalp Studio Platform:

1. [Sign Up and Log In to Kalp Studio Platform](https://doc.kalp.studio/Getting-started/Onboarding/How-to-Sign-Up-and-Log-In-to-Kalp-Studio-Platform/)

2. [Deploy a Smart Contract on KALP Studio](https://doc.kalp.studio/Dev-documentation/Kalp-DLT/Smart-Contract-Write-Test-Deploy-Interact/Deploy-the-smart-contract/)


---

## Checkpoint 9: 🔒 Interacte with smart contract

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

![alt text](image.png)

---

**After that Click on **Check Params** , the routing details and parameters should Look Like this.**

![alt text](image-1.png)

---

* The code snippet requires an API key for authorization in an API post request.
 * The API key can be generated using an API key generation process.

 **That would look like this in Kalp Studio**



![alt text](image-2.png)

---
**Follow the process for api auth key generation**

![alt text](image-11.png)

---

#### This code snippet demonstrates how to use the `initialize` function and make a POST request using Postman.

#### Instructions
1. Copy the route of the `initialize` function and paste it into the URL box in Postman.

2. Copy the parameters **(Check Params)** of the `initialize` function and paste them into the body of Postman, selecting the raw json type.
3. Pass the custom value for the name and symbol of the NFT in the parameters. See the example **screen shot**  on line number 6 and 7
 #### Parameters
- `name`: The custom value for the name of the NFT.
- `symbol`: The custom value for the symbol of the NFT.

Note: Make sure to replace any placeholders or variables in the copied code with actual values before making the request. 

![alt text](image-4.png)

---

#### Select header in postman 
<!-- write ``` x-api ``` in key part and paste the auth key in value you got after apikeygeneration side bar in kalp studio -->

This code snippet demonstrates how to select a header in Postman and set the `x-api` key with the value obtained from the API key generation sidebar in Kalp Studio.

## Steps

1. In the request headers section.
4. Set the key as `x-api`.
5. Paste the auth key obtained from the API key generation sidebar in Kalp Studio as the value for the `x-api` header.

Note: Make sure you have the necessary permissions and access to the API key generation sidebar in Kalp Studio.

![alt text](image-5.png)

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

![alt text](image-7.png)

---

#### This code snippet demonstrates how to use the tokenURI function and make a GET request using Postman.

##### Instructions
- Copy the route of the tokenURI function and paste it into the URL box in Postman.

- Copy the parameters (Check Params) of the tokenURI function and paste them into the URL parameters section in Postman.

- Pass the custom value for the token ID in the parameters. See the example screen shot on line number 6 and 7.

###### Parameters
- tokenId: The custom value for the ID of the NFT.

Note: Make sure to replace any placeholders or variables in the copied code with actual values before making the request.

![alt text](image-8.png)

---

#### This code snippet demonstrates how to use the ownerOf function and make a GET request using Postman.

##### Instructions
- Copy the route of the ownerOf function and paste it into the URL box in Postman.

- Copy the parameters (Check Params) of the ownerOf function and paste them into the URL parameters section in Postman.
- Pass the custom value for the token ID in the parameters. See the example screen shot on line number 6 and 7.

##### Parameters
- tokenId: The custom value for the ID of the NFT.

Note: Make sure to replace any placeholders or variables in the copied code with actual values before making the request.

![alt text](image-9.png)

---

This README serves as an overview for developing and deploying KRC721 NFTs using Go on the Kalp blockchain. For additional details, refer to the Kalp SDK documentation.
Step by Step to Deploy a Smart Contract on KALP Studio