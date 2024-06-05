# Ondo Finance @ Code4rena (2024-03)

## `Disclaimer` 

These are my personal interpretations and analyses of the findings report from the audit competition I participated in. They are for my own understanding, self-reflection, and improvement. The content written here MUST NOT be interpreted as facts.

If you are interested in the official findings report, you should refer to this link: https://code4rena.com/reports/2024-03-ondo-finance.

## `Findings: High (1), Medium (4)`
### `>> H-01 <<`
`- Summary -`

The price of OUSG is based on fiat USD (the protocol team provides their own oracle), while the transfer and receipt are priced in USDC token (for obvious reasons, as only via token can value transfer occur on-chain).

`- Details -`

Now, here's the problem: Users are buying and selling in terms of fiat USD value in exchange for the USDC token. USD and USDC do not always maintain a 1:1 ratio. The [depeg event](https://www.coindesk.com/consensus-magazine/2023/04/02/usdcs-depeg-laid-bare-the-risks-traditional-finance-poses-to-stablecoins/) occurred last year, so there is a strong possibility it will happen again.

If the price of USDC:USD is 1 : 1.2, the exploiter can buy 20% more OUSG than its USD value equivalent and then sell it back when the price returns to 1:1.

`- Mitigation -`

Integrate a reliable oracle like Chainlink to track the value of USDC. If the spread is more than the tolerance set by the protocol team, then pause the trading.

`- Reference Report -`
- https://github.com/code-423n4/2024-03-ondo-finance-findings/issues/161 (To me, this is the best report.)
- https://github.com/code-423n4/2024-03-ondo-finance-findings/issues/278
- https://github.com/code-423n4/2024-03-ondo-finance-findings/issues/297

## MEDIUMs (4)
### `>> M-01 <<` 

Not interested in analyzing this at the moment, but you can refer to the official report: https://github.com/code-423n4/2024-03-ondo-finance-findings/issues/309

### `>> M-02 <<`

Not interested in analyzing this at the moment, but you can refer to the official report: https://github.com/code-423n4/2024-03-ondo-finance-findings/issues/306

### `>> M-03 <<`
`- Summary -`

The 'minimumRedemptionAmount' limit of 50k USDC will temporarily trap user funds and incur fees upon full redemption.

`- Details -`

If a user with 100k USDC funds decides to withdraw 60k USDC, leaving 40k USDC in the account, attempting to withdraw the remaining 40k will trigger a revert due to the 'minimumRedemptionAmount' limit of 50k USDC.

The user will then be required to deposit the 'minimumDepositAmount' of 100k USDC, resulting in a total holdings of 140k USDC. Now, the user can withdraw the entire 140k USDC amount to reclaim all of his/her holdings.

Producing an additional 100k just to redeem the remaining 40k will incur fees, thus making it a medium severity issue.

`- Mitigation -`

Reduce the minimum redemption amount to a trivial value.

`- Reference Report -`
- https://github.com/code-423n4/2024-03-ondo-finance-findings/issues/142

### `>> M-04 <<`
Not interested in analyzing this at the moment, but you can refer to the official report: https://github.com/code-423n4/2024-03-ondo-finance-findings/issues/32