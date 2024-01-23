
# Liquid Staking with Liquid Collective

## What is Liquid Collective?

Liquid Collective is a liquid staking standard focusing on security.

In order to interact with the Liquid Collective protocol, your address needs to be added to an approved allowlist via the Fireblocks team. Please reach out to Fireblocks support or your Account Manager to get your address(s) added.

Once your wallet address(s) are added, you will be able to stake and redeem ETH, by interacting with the Liquid Collective smart contract.

The Liquid Collective issues a receipt token, LsETH. For more information on LsETH, checkout the [LsETH docs page](https://docs.liquidcollective.io/eth/tokenomics/lseth).

## Github repo usage

To use the functions, uncomment the line that calls each function. For example, if you'd like to run `createRedeemRequest()`, be sure it is uncommented, and run:

```node app.js```

## Stake ETH

Using the console, either in a whitelisted external address, or a one-time address, create a transfer and
send desired ETH to stake to:

[Mainnet](https://etherscan.io/tokenholdings?a=0x8c1BEd5b9a0928467c9B1341Da1D7BD5e10b6549) or here on [Goerli Testnet](https://goerli.etherscan.io/address/0x3ecCAdA3e11c1Cc3e9B5a53176A67cc3ABDD3E46).

Note the Liquid Collective uses a TUP Proxy pattern, so the proxy contract (where you interact) will be different from implementation contract. Below is a table of the Proxy vs. implementation contracts:

| Ethereum Network | Proxy                                      | Implementation                             |
| ---------------- | ------------------------------------------ | ------------------------------------------ |
| Goerli           | 0x3ecCAdA3e11c1Cc3e9B5a53176A67cc3ABDD3E46 | 0xF32fC26C9604a380c311e7eC0c5E545917e7934f |
| Mainnet          | 0x8c1BEd5b9a0928467c9B1341Da1D7BD5e10b6549 | 0x48D93d8C45Fb25125F13cdd40529BbeaA97A6565 |

TODO Eric to add screenshot Transfer and LsETH in wallet. 

## Unstaking

Unstaking will require two separate smart contract calls.

1. Submitting a Redeem Request
2. Claim Request Redeem once the redemption is ready for claiming.

*Redeem Request:*

When submitting a Redemption request, a redeem request ID will be returned. The best way to retrieve this ID is by opening a websocket server to listen for contract events emitted from the RedeemManager. We've added a websocket file that you can use for testing `websocket.js`. The `websocket.js` uses an Infura websocket API, you can use Infura or any other provider that extends websocket access.

The websocket will point to the Redeem Manger contract. Below are the contract address details:

| Ethereum Network | Proxy                                      | Implementation                             |
| ---------------- | ------------------------------------------ | ------------------------------------------ |
| Goerli           | 0x0693875efbF04dDAd955c04332bA3324472DF980 | 0x653b549554669a06bb9e260b9f2c0a54f5d7e722 |
| Mainnet          | 0x080b3a41390b357Ad7e8097644d1DEDf57AD3375 | 0x423ce5282c460eed5fe0786b4d47d2c2a4ef3721 |

After configuring `websocket.js` , start your websocket by running:

```js
node websocket.js
```

The websocket app will listen for the following events:
- RequestedRedeem
- ReportedWithdrawal
- SatisfiedRedeemRequest
- ClaimedRedeemRequest

In a separate terminal (leaving your websocket app running),uncomment and call the function `createRedeemRequest`. Be sure that the amount to claim, and the contract address is updated accordingly.

Be sure the smart contract is whitelisted, and you should see it execute in the fireblocks console as it fires. Approve the transaction using your mobile device.

In your `app.js` you should see a transaction hash returned. In your `websocket.js` terminal you should see a redeem request event emitted, containing the redemption request ID. Below is an example response.

```json
RequestedRedeem event
Owner of redeem <WALLET ADDRESS>
Height 4980971589444881813
Amount of LsETH to redeem 10000000
Maximum amount of ETH to redeem 10212074
Request Redeem ID 18
```

Keep this ID, example of 18. It will take at least 24hours for the redemption request to be resolved. You can check the status of the redemption request by calling the function resolveRedeemRequest(). Add your redeem request ID to the array and call the function. Any number greater than 0, means your redemption is satisfied (partially or fully). The number returned is the withdrawal event ID and is used when making a claim.

*Claiming*:

Uncomment and call the function `createClaimRedeemRequest`. Be sure that the request ID and withdrawal ID is updated with the values from the above API endpoint.

See the smart contract function execute and your ETH balance reflect the claim successfully!

TODO Eric to add screenshot of console. 

## Links/Resources

- Postman link
- [Fireblocks web3 provider](https://github.com/fireblocks/fireblocks-web3-provider/)
