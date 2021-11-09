## Thorswap Monitor

[Thorswap](https://thorswap.finance/) is a leading multi-chain DEX. Powered by THORchain, the dex allows users to swap assets directly between chains, without wrapping them. 

Using Parsiq, Thorswap swaps can be monitored and analyzed. 

Visualizations: https://docs.google.com/spreadsheets/d/1iXEtRwUov738w99vwW-iybK_MxsCMsQAPQf5dPdZm_U/
 
Data Feed: https://docs.google.com/spreadsheets/d/1iXEtRwUov738w99vwW-iybK_MxsCMsQAPQf5dPdZm_U/

Query used ([query.parsql](/query.parsiql))

```
stream QuickMonitor
from TokenTransfers
where @to == ETH.address("0xc145990e84155416144c532e31f89b840ca8c2ce") || @from == ETH.address("0xc145990e84155416144c532e31f89b840ca8c2ce")
process
  let isCrystalProviderDataReady = false
  let crystalProviderData = {percentRiskScore: "0", decimalRiskScore: {value: 0, decimals: 0}, targetName: "", targetType: "", signals: {atm: "", darkMarket: "", darkService: "", exchange: "", gambling: "", illegalService: "", marketplace: "", miner: "", mixer: "", payment: "", ransom: "", riskyExchange: "", scam: "", stolenCoins: "", trustedExchange: "", wallet: ""}}
  let executionTarget = ETH.address("0xc145990e84155416144c532e31f89b840ca8c2ce")
  let counterpartyAddress = 0x0
  if @to == executionTarget then counterpartyAddress = @from
  else counterpartyAddress = @to end
  let txInfo = { txHash: @tx_hash }
  let cryptorankFiatRate = getRate(@erc20.symbol)
    let fiat_value = @value * cryptorankFiatRate.value
    let fiat_decimals = @erc20.decimals + cryptorankFiatRate.decimals
  if @verified == true then
  emit { @from, @to, @value, txInfo, symbol: @erc20.symbol, decimals: @erc20.decimals, fiat_value, fiat_decimals, blockNumber: @block_number }
end
end

```
