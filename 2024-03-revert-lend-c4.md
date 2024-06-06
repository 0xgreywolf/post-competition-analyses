# Revert Lend @ Code4rena (2024-03)
## `DISCLAIMER` 

These are my personal interpretations and analyses of the findings report from the audit competition I participated in. They are for my own understanding, self-reflection, and improvement. The content written here MUST NOT be interpreted as facts.

If you are interested in the official findings report, you should refer to this link: https://code4rena.com/reports/2024-03-revert-lend.

## `TOTAL FINDINGS: HIGH (6), MEDIUM (27)`
## `[H-01]`
### `Summary`

Any token can be deposited into the vault alongside a permit2 signature, and USDC assets (the vault asset) can be withdrawn in exchange.

### `Details`

The code snippet 'permit2.permitTransferFrom' is found in the [`V3Vault::_deposit`](https://github.com/code-423n4/2024-03-revert-lend/blob/62ad70e634082930aed861e153d2c1e0cc94e44e/src/V3Vault.sol#L877-L917) function, where it transfers assets from the sender to the V3Vault."

```solidity
if (permitData.length > 0) {
    (ISignatureTransfer.PermitTransferFrom memory permit, bytes memory signature) =
        abi.decode(permitData, (ISignatureTransfer.PermitTransferFrom, bytes));
    permit2.permitTransferFrom(
        permit, ISignatureTransfer.SignatureTransferDetails(address(this), assets), msg.sender, signature
    );
} else {
    // fails if not enough token approved
    SafeERC20.safeTransferFrom(IERC20(asset), msg.sender, address(this), assets);
}
```
But specifically within the 'if' block, there is no validation for the 'asset' address, which means any token can be deposited in exchange for USDC.

### `Mitigation`

Parameterize and validate the `asset` address.

```diff
-- function _deposit(address receiver, uint256 amount, bool isShare, bytes memory permitData)
++ function _deposit(address receiver, address asset, uint256 amount, bool isShare, bytes memory permitData)
        internal
        returns (uint256 assets, uint256 shares)
{
    ...

    if (permitData.length > 0) {
        (ISignatureTransfer.PermitTransferFrom memory permit, bytes memory signature) =
            abi.decode(permitData, (ISignatureTransfer.PermitTransferFrom, bytes));

++  if (permit.permitted.token != asset, "Error: token not supported");    
        
        permit2.permitTransferFrom(
            permit, ISignatureTransfer.SignatureTransferDetails(address(this), assets), msg.sender, signature
        );
    } else {
        // fails if not enough token approved
        SafeERC20.safeTransferFrom(IERC20(asset), msg.sender, address(this), assets);
    }
    
    ...
}
```
### `Reference Report`
- https://github.com/code-423n4/2024-03-revert-lend-findings/issues/368
- https://github.com/code-423n4/2024-03-revert-lend-findings/issues/433
- https://github.com/code-423n4/2024-03-revert-lend-findings/issues/23


## `[H-02]`
### `Summary`

### `Details`

### `Mitigation`

### `Reference Report`
