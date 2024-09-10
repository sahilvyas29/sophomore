
---

### 🚩 Challenge 2: 🔏 Non Fungible Token (NFT) with BuildThon
# BuildThon

## Description
BuildThon is a Go-based KRC-721 NFT smart contract  allows the creation, management, and transfer of unique digital assets (NFTs). Each token represents a unique item and is stored on the blockchain with a one-to-one relationship between the token ID and its metadata. The contract complies with the KRC-721 standard, enabling interoperability with NFT marketplaces, wallets, and other dapps.

## What is KRC-721?

The KRC-721 introduces a standard for NFT, in other words, this type of Token is unique and can have different value than another Token from the same Smart Contract, maybe due to its age, rarity or even something else like its visual. Wait, visual?

Yes! All NFTs have a uint256 variable called tokenId, so for any KRC-721 Contract, the pair contract address, uint256 tokenId must be globally unique. That said, a dapp can have a "converter" that uses the tokenId as input and outputs an image of something cool, like zombies, weapons, skills or amazing kitties!


### 🚀 Key Features
- 🛠 Easy-to-use APIs for smart contract development.
- 🔒 Secure and efficient contract execution.
- 🧩 Integration with popular blockchain platforms.
- 🖥 Real-time monitoring and logging.
- 📚 Comprehensive documentation and examples.

---

## Checkpoint 0: 📦 Installation

Before you begin, ensure you have the following:

- 🖥 Go version `>=1.19` but `<1.20`.
- ⚙️ Installed and configured Kalp SDK.

To install BuildThon, follow these steps:

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

## Checkpoint 1: 🏗 Token Contract Functions

### Initialize Token Contract
This function sets up the name and symbol of the KRC721 token collection.

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

---

### Checkpoint 2: 🖨 Minting NFTs
Mint new NFTs with a unique token ID and metadata URI using `MintWithTokenURI`.

```go
func (c *TokenERC721Contract) MintWithTokenURI(ctx kalpsdk.TransactionContextInterface, tokenId string, tokenURI string) (*Nft, error) {
    nft := &Nft{TokenId: tokenId, Owner: /* Owner's identity */, TokenURI: tokenURI}
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    nftBytes, _ := json.Marshal(nft)
    ctx.PutStateWithoutKYC(nftKey, nftBytes)
    return nft, nil
}
```

---

### Checkpoint 3: 🔥 Burning NFTs
Permanently delete an NFT from the blockchain using `Burn`.

```go
func (c *TokenERC721Contract) Burn(ctx kalpsdk.TransactionContextInterface, tokenId string) (bool, error) {
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    ctx.DelStateWithoutKYC(nftKey)
    return true, nil
}
```

---

## Checkpoint 4: 🔄 Transferring NFTs
Transfer ownership of an NFT from one account to another using `TransferFrom`.

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

---

## Checkpoint 5: ✏️ Approval & Authorization

### Approve
Authorize another account to transfer an NFT on behalf of the owner.

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

### SetApprovalForAll
Authorize an operator to manage all NFTs owned by a specific account.

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

---

## Checkpoint 6: 📊 Read Functions

### BalanceOf
Get the number of NFTs owned by a specific account.

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

### OwnerOf
Find out who owns a specific NFT.

```go
func (c *TokenERC721Contract) OwnerOf(ctx kalpsdk.TransactionContextInterface, tokenId string) (string, error) {
    nft, err := _readNFT(ctx, tokenId)
    if err != nil {
        return "", err
    }
    return nft.Owner, nil
}
```

---

## Checkpoint 7: 🏗 Contract Metadata

### Name
Get the name of the token collection.

```go
func (c *TokenERC721Contract) Name(ctx kalpsdk.TransactionContextInterface) (string, error) {
    bytes, err := ctx.GetState(nameKey)
    if err != nil {
        return "", err
    }
    return string(bytes), nil
}
```

### Symbol
Get the symbol of the token collection.

```go
func (c *TokenERC721Contract) Symbol(ctx kalpsdk.TransactionContextInterface) (string, error) {
    bytes, err := ctx.GetState(symbolKey)
    if err != nil {
        return "", err
    }
    return string(bytes), nil
}
```

---

## Checkpoint 8: 🔒 Security & Events
The KRC721 contract emits key events like `Transfer`, `Approval`, and `ApprovalForAll` to notify external systems. It includes comprehensive security checks, such as authorization to prevent unauthorized actions.

---

This README serves as an overview for developing and deploying KRC721 NFTs using Go on the Kalp blockchain. For additional details, refer to the Kalp SDK documentation.
