# Creditcoin-Scripts
Misc PowerShell scripts for Creditcoin 2.0 (Substrate-based implementation).

## Run a miner and output the logs to a file and the screen

```
.\creditcoin-node.exe `
  --validator `
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" `
  --bootnodes /dns4/ccmn-bootnode.koreacentral.azurecontainer.io/tcp/30333/p2p/12D3KooWAEgDL126EUFxFfdQKiUhmx3BJPdszQHu9PsYsLCuavhb `
  --chain main `
  --mining-key {your-key-here} `
  --base-path data `
  --prometheus-external 2>&1 `
  | ForEach-Object { $_ -replace "ΓÇª","..." -replace "Γ¼ç","↓" -replace "Γ¼å","↑" -replace "(ΓÜá∩╕Å|Γ£î∩╕Å|Γ¥ñ∩╕Å|≡ƒÆ╛|≡ƒöì|ΓÜá∩╕Å|≡ƒÆñ|Γ£¿|≡ƒÖî0|≡ƒÄü|≡ƒÖî|ΓÜÖ∩╕Å|Γ£à|Γ¢ô|≡ƒÅ╖|≡ƒôª|≡ƒôï|πÇ╜∩╕Å)","-" -replace "  "," " } `
  | Tee-Object -Append .\miner.log
```

Example output: 
> 2022-04-23 17:37:12 - Imported #193155 (0xc926...8325)  
2022-04-23 17:37:12 - Starting consensus session on top of parent 0xc9266293608f85362f181d9e5bf0f8a5af3162d586f38b4ffcbcf705ed498325  
2022-04-23 17:37:12 - Prepared block for proposing at 193156 (6 ms) [hash: 0x1c704fd8c770609ecc821354868d108fa4b707a3c3e88f8ceb3ef854dcb6616a; parent_hash: 0xc926...8325; extrinsics (1): [0xb26d...7d58]]  
2022-04-23 17:37:12 - Idle (3 peers), best: #193155 (0xc926...8325), finalized #0 (0xf5c3...529b), ↓ 9.0kiB/s ↑ 6.5kiB/s

## Run a miner and output the logs to a file and mined blocks only to the screen

```
.\creditcoin-node.exe `
  --validator `
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" `
  --bootnodes /dns4/ccmn-bootnode.koreacentral.azurecontainer.io/tcp/30333/p2p/12D3KooWAEgDL126EUFxFfdQKiUhmx3BJPdszQHu9PsYsLCuavhb `
  --chain main `
  --mining-key {your-key-here} `
  --base-path data `
  --prometheus-external 2>&1 `
  | ForEach-Object { $_ -replace "ΓÇª","..." -replace "Γ¼ç","↓" -replace "Γ¼å","↑" -replace "(ΓÜá∩╕Å|Γ£î∩╕Å|Γ¥ñ∩╕Å|≡ƒÆ╛|≡ƒöì|ΓÜá∩╕Å|≡ƒÆñ|Γ£¿|≡ƒÖî0|≡ƒÄü|≡ƒÖî|ΓÜÖ∩╕Å|Γ£à|Γ¢ô|≡ƒÅ╖|≡ƒôª|≡ƒôï|πÇ╜∩╕Å)","-" -replace "  "," " } `
  | Tee-Object -Append .\miner.log `
  | Where-Object { $_ -like "*Successfully mined*" }
```

Example output: 
> 2022-04-23 17:39:35 - Successfully mined block on top of: 0x88ad...b7b8  
2022-04-23 17:39:38 - Successfully mined block on top of: 0xc341...608e  
2022-04-23 17:39:42 - Successfully mined block on top of: 0x9db2...98a3  
2022-04-23 17:39:43 - Successfully mined block on top of: 0x8ff6...1c83

## Monitor the miner log and output how many blocks were mined

```
while($true) {
  $x = Get-Content .\miner.log | Where-Object {$_ -like "*Successfully mined*"} | sort -Unique | Out-File .\mined.blocks
  $count = Get-Content .\mined.blocks |  Measure-Object | Select -Exp Count
  $ctc = ($count * 28).ToString('N0')
  "$(Get-Date): Mined $count blocks ($ctc CTC)"
  Start-Sleep 60
}
```

Example output:
> 04/23/2022 17:36:57: Mined 105 blocks (2,940 CTC)  
04/23/2022 17:37:57: Mined 125 blocks (3,500 CTC)  
04/23/2022 17:38:57: Mined 132 blocks (3,696 CTC)  
04/23/2022 17:39:57: Mined 142 blocks (3,976 CTC)
