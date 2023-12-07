# SWEEP command
This command transfers all `token` balances and/or ownerships to a destination address

## PARAMS
| Name              | Type   | Description              			                             |
| ----------------- | ------ | ----------------------------------------------------------------- |
| `VERSION`         | String | Broadcast Format Version                                          |
| `DESTINATION` 	| String | address where `token` shall be swept                              |
| `SWEEP_BALANCES` 	| String | Indicates if address `token` balances should be swept (default=1) |
| `SWEEP_OWNERSHIP` | String | Indicates if address `token` balances should be swept (default=1) |
| `MEMO` 			| String | Optional memo to include                                          |

## Formats

### Version `0`
- `VERSION|DESTINATION|SWEEP_BALANCES|SWEEP_OWNERSHIP|MEMO`

## Examples
```
bt:SWEEP|0|cosmos1f0wvhastnvu3ynyspvv8de3rpe66jmhwtthdrv|1|1
This example sweeps both token balances and ownership from the broadcasting address to cosmos1f0wvhastnvu3ynyspvv8de3rpe66jmhwtthdrv
```

```
bt:SWEEP|0|1BoogrfDADPLQpq8LMASmWQUVYDp4t2hF9|0|1`
This example sweeps only token ownership from the broadcasting address to cosmos1f0wvhastnvu3ynyspvv8de3rpe66jmhwtthdrv
```

## Rules

## Notes
