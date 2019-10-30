---
tags: [solidity]
---

```solidity
function getAuction(uint256 _tokenId) external constant returns(
    address seller,
    uint256 sellPrice
) {
    Auction storage auction = tokenIdToAuction[_tokenId];
    return (
        auction.seller,
        auction.sellPrice
    );
}
```
