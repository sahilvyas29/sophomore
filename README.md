🚩 Challenge 2: 🔏 Non Fungible Token
# BuildThon

## Description
BuildThon is a Go-based framework for developing and managing smart contracts. It provides a robust set of tools and libraries to facilitate the creation, deployment, and management of smart contracts on blockchain platforms.

## Features
- Easy-to-use APIs for smart contract development
- Secure and efficient contract execution
- Integration with popular blockchain platforms
- Real-time monitoring and logging
- Comprehensive documentation and examples

## Installation
To install BuildThon, follow these steps:

1. Clone the repository:
```sh
   git clone https://github.com/yourusername/buildthon.git
```

2. Navigate to the project directory:
```sh
   cd buildthon
```

3. Install the dependencies
```sh  
   go mod tidy
```
---

# KRC721 Token Contract Function Explanation

- **Go Programming Language**: A basic understanding of Go.
- **Blockchain & NFT Concepts**: Familiarity with blockchain technology and NFTs.
- **Kalp SDK**: Installed and configured Kalp SDK environment.

> **Note**: The Kalp SDK is compatible with Go versions `>=1.19` and `<1.20`.

## Token Contract Functions

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

### MintWithTokenURI
Mint new NFTs with a unique token ID and metadata URI.

```go
func (c *TokenERC721Contract) MintWithTokenURI(ctx kalpsdk.TransactionContextInterface, tokenId string, tokenURI string) (*Nft, error) {
    nft := &Nft{TokenId: tokenId, Owner: /* Owner's identity */, TokenURI: tokenURI}
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    nftBytes, _ := json.Marshal(nft)
    ctx.PutStateWithoutKYC(nftKey, nftBytes)
    return nft, nil
}
```

### Burn
Permanently delete an NFT from the blockchain.

```go
func (c *TokenERC721Contract) Burn(ctx kalpsdk.TransactionContextInterface, tokenId string) (bool, error) {
    nftKey, _ := ctx.CreateCompositeKey(nftPrefix, []string{tokenId})
    ctx.DelStateWithoutKYC(nftKey)
    return true, nil
}
```

### TransferFrom
Transfer ownership of an NFT from one account to another.

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

## Read Functions

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

### GetApproved
Check which account has transfer approval for a specific NFT.

```go
func (c *TokenERC721Contract) GetApproved(ctx kalpsdk.TransactionContextInterface, tokenId string) (string, error) {
    nft, err := _readNFT(ctx, tokenId)
    if err != nil {
        return "", err
    }
    return nft.Approved, nil
}
```

### IsApprovedForAll
Determine whether an operator has full approval to manage all NFTs of an account.

```go
func (c *TokenERC721Contract) IsApprovedForAll(ctx kalpsdk.TransactionContextInterface, owner string, operator string) (bool, error) {
    approvalKey, _ := ctx.CreateCompositeKey(approvalPrefix, []string{owner, operator})
    approvalBytes, err := ctx.GetState(approvalKey)
    if len(approvalBytes) < 1 {
        return false, nil
    }
    approval := new(Approval)
    _ = json.Unmarshal(approvalBytes)
    return approval.Approved, err
}
```

## Contract Metadata

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

### TokenURI
Get the metadata URI for an NFT.

```go
func (c *TokenERC721Contract) TokenURI(ctx kalpsdk.TransactionContextInterface, tokenId string) (string, error) {
    nft, err := _readNFT(ctx, tokenId)
    if err != nil {
        return "", err
    }
    return nft.TokenURI, nil
}
```

### TotalSupply
Get the total number of NFTs minted in the collection.

```go
func (c *TokenERC721Contract) TotalSupply(ctx kalpsdk.TransactionContextInterface) int {
    iterator, _ := ctx.GetStateByPartialCompositeKey(nftPrefix, []string{})
    totalSupply := 0
    for iterator.HasNext() {
        iterator.Next()
        totalSupply++
    }
    return totalSupply
}
```

## Events & Security
The KRC721 contract emits events like `Transfer`, `Approval`, and `ApprovalForAll` to notify external systems. It includes comprehensive security checks, such as authorization, to prevent unauthorized actions.

---
### challenges


This README serves as an overview for developing and deploying KRC721 NFTs using Go on the Kalp blockchain. For additional details, refer to the Kalp SDK documentation.
