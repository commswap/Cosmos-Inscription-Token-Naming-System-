CHANGELOG
---
0.10.1
- set MIN_TOKEN_SUPPLY to 0.000000000000000001
- Prevent LOCK_SUPPLY if no supply is issued 
- Prevent setting MAX_SUPPLY below current SUPPLY
- Updated getTokenSupply() to use CAST in sql queries
- Updated getAssetInfo() to only lookup assets using name 

0.10.0 - Initial Release
- ISSUE support
- LIST support
- MINT support
- SEND support
