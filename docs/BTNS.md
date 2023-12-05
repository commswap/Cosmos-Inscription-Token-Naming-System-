        Title: Broadcast Token Naming System (BTNS)
        Author: Jeremy Johnson (J-Dog)
        Status: Accepted
        Type: Informational
        Created: 2023-05-14

# Abstract
Establish new token naming system via Broadcasts

# Motivation
Allow experimentation with an alternate token naming system

# Rationale
Counterparty has established a beautiful asset naming system which allows for registration of unique assets and subassets, but it requires the use of XCP as an anti-spam mechanism. One of the criticizms of Counterparty has been that it requires XCP in order to use the platform. This is untrue, as you can use Counterparty and issue a numeric asset using only BTC. However, it is clear that some people see this "anti-spam" mechanism as unnecessary, and there is a desire for possible alternate naming systems to be explored.

This proposal establishes a new token naming system which will allow additional experimentation with "tokens" on Bitcoin via the Counterparty `broadcast` system.

By establishing 3 pre-defined broadcast formats, users can `DEPLOY`, `MINT`, and `TRANSFER` tokens. With these 3 functions we can create tokens, allow users to mint them in a decentralized "fair" way, and allow for the moving of these new tokens between addresses. 

This spec can be extended in the future to allow for additional options and formats.

This spec was inspired in part by BRC-20 and SRC-20 and seeing the desire to experiment with new token naming systems.

Additional BTNS specs, indexers, and development tools are available at the official BTNS repo at: https://github.com/jdogresorg/Broadcast-Token-Naming-System

# Definitions

- `token` - A virtual token which was created via a `DEPLOY` or `MINT` format `broadcast` transaction
- `broadcast` - A general purpose transaction type which allows broadcasting of a message to the Counterparty platform
- `DEPLOY` - A specially formatted `broadcast` which registers a `token` name for usage
- `MINT` - A specially formatted `broadcast` which allows for creation/minting of token supply 
- `TRANSFER` - A specially formatted `broadcast` which can move a `token` between users

# Specification
This spec defines 3 formats `DEPLOY`, `MINT`, and `TRANSFER` which will allow for creation of tokens, supply, and sending between users

## Project Prefix
In order for different projects to experiment with features in the Broadcast Token Naming System (BTNS) and not collide with one another, each project should establish a unique prefix for their project to use in their broadcasts.

The default BTNS prefix which should be used for BTNS transactions is `BTNS` and `BT`. All BTNS actions will begin with `btns:` or `bt:` (case insensitive)

## Project Versioning
Blockchain development moves fast, and quite often there is a need to change specs and switch to a new version immediately. The BTNS has a novel way of handling versioning, by using the `broadcast` `value` field to indicate what version of a spec a BTNS `broadcast` is using.

The default BTNS version is `0` when no `broadcast` `value` is specified

To specify a specific version of a BTNS spec, you can specify the version number in the `broadcast` `value` field

## `DEPLOY` format
This format allows one to create a token and specify the following information about it

- `TICK` - 1 to 1024 characters in length (see rules below ) (required)
- `MAX_SUPPLY` - Maximum token supply (max: 18,446,744,073,709,551,615 - commas not allowed)
- `MAX_MINT` - Maximum amount of supply a `MINT` transaction can issue
- `DECIMALS` - Number of decimal places token should have (max: 18, default: 0)
- `ICON` - URL to a an icon to use for this token (48x48 standard size)
- `MINT_SUPPLY` - Amount of token supply to mint in immediately (default:0)
- `TRANSFER` - Address to transfer ownership of the `token` to (owner can perform future actions on token)
- `TRANSFER_SUPPLY` - Address to transfer `MINT_SUPPLY` to (mint initial supply and transfer to address)

**Broadcast Format:**
`bt:DEPLOY|TICK|MAX_SUPPLY|MAX_MINT|DECIMALS|ICON|MINT_SUPPLY|TRANSFER|TRANSFER_SUPPLY`

**Example 1:**
`bt:DEPLOY|JDOG|1000|1|0`
The above example issues a JDOG token with a max supply of 1000, and a maximum mint of 1 JDOG per `MINT`

**Example 2:**
`bt:DEPLOY|JDOG|1000|1|0|http://j-dog.net/images/JDOG_icon.png`
The above example issues a JDOG token with a max supply of 1000, and a maximum mint of 1 JDOG per `MINT` and associates an `ICON` with the `token`

**Example 3:**
`bt:DEPLOY|BRRR|10000000000000000000|10000000000000|0|https://j-dog.net/images/BRRR_icon.png|100`
The above example issues a BRRR token with a max supply of 1 Quandrillion supply and a maximum mint of 1 Trillion BRRR per `MINT`, associates an `ICON` with the `token`, and immediately mints 100 BRRR to the broadcasting address.

**Example 4:**
`bt:DEPLOY|TEST|100|1|0||1|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example issues a TEST token with a max supply of 100, and a maximum mint of 1 TEST per `MINT`. This also mints 1 TEST token, and transfers ownership AND initial token supply to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

### Rules
- `broadcast` `status` must be `valid`
- First `TICK` `DEPLOY` will be considered as valid.
- Additional `TICK` `DEPLOY` transactions after first valid `TICK` `DEPLOY`, will be considered invalid and ignored, unless broadcast from `token` owners address
- If `TICK` contains any unicode characters, then `TICK` should be `base64` encoded
- Allowed characters in `TICK`:
   - Any word character (alphanumeric characters and underscores)
   - Special characters: ~!@#$%^&*()_+\-={}[\]\\:<>.?/
   - empty space
   - Most printable emojis in U+1F300 to U+1F5FF
- Special characters pipe `|` and semicolon `;` are **NOT** to be used in `TICK` names 

## `MINT` format
This format allows one to mint token supply

- `TICK` - `token` name registered with `DEPLOY` format (required)
- `AMOUNT` - Amount of tokens to mint (required)
- `DESTINATION` - Address to transfer tokens to

**Broadcast Format:**
`bt:MINT|TICK|AMOUNT|DESTINATION`

**Example 1:**
`bt:MINT|JDOG|1`
The above example mints 1 JDOG `token` to the broadcasting address

**Example 2:**
`bt:MINT|BRRR|10000000000000|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example mints 10,000,000,000,000 BRRR tokens and transfers them to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev 

### Rules
- `broadcast` `status` must be `valid`
- `token` supply may be minted until `MAX_SUPPLY` is reached.
- Transactions that attempt to mint supply beyond `MAX_SUPPLY` shall be considered invalid and ignored.


## `TRANSFER` format
This format allows one to transfer or send a `token` between addresses

- `TICK` - `token` name registered with `DEPLOY` format (required)
- `AMOUNT` - Amount of tokens to send (required)
- `DESTINATION` - Address to transfer tokens to (required)

This format also allows for repeating `AMOUNT` and `DESTINATION` to enable multiple transfers in a single transaction

**Broadcast Format:**
`bt:TRANSFER|TICK|AMOUNT|DESTINATION`

**Broadcast Format2:**
`bt:TRANSFER|TICK|AMOUNT|DESTINATION|AMOUNT|DESTINATION`

**Broadcast Format3:**
`bt:TRANSFER|TICK|AMOUNT|DESTINATION|TICK|AMOUNT|DESTINATION`

**Example 1:**
`bt:TRANSFER|JDOG|1|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example sends 1 JDOG token to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

**Example 2:**
`bt:TRANSFER|BRRR|5|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev`
The above example sends 5 BRRR tokens to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev

**Example 3:**
`bt:TRANSFER|BRRR|5|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev|1|1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9`
The above example sends 5 BRRR tokens to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev and 1 BRRR token to 1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9

**Example 4:**
`bt:TRANSFER|BRRR|5|1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev|TEST|1|1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9`
The above example sends 5 BRRR tokens to 1JDogZS6tQcSxwfxhv6XKKjcyicYA4Feev and 1 TEST token to 1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9

### Rules
- `broadcast` `status` must be `valid`
- A `token` transfer shall only be considered valid if the broacasting address has balances of the `token` to cover the transfer `AMOUNT`
- A `token` transfer that does _not_ have `AMOUNT` in the broadcasting address shall be considered invalid and ignored.
- A valid `token` transfer will deduct the `token` `AMOUNT` from the broadcasting addresses balances
- A valid `token` tranfer will credit the `token` `AMOUNT` to the `DESTINATION` address or addresses

# Copyright
This document is placed in the public domain.
