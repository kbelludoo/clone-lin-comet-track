# clone-lin-comet-track

Experimental i64-scale LIN clone of [compound-finance/comet](https://github.com/compound-finance/comet) `contracts/CometWithExtendedAssetList.sol` (`accrueInternal` trackingSupplyIndex / `divBaseWei`) plus `CometCore.sol` scales (`FACTOR_SCALE=1e18`, `BASE_INDEX_SCALE=1e15`, `BASE_ACCRUAL_SCALE=1e6`).

- **LIN clone:** [`src/lin_comet_track.lin`](src/lin_comet_track.lin)
- **Proofs (results only) live in lin-open:** https://github.com/kbelludoo/lin-open
- **Event receipt:** `docs/events/EVENT_COMET_TRACK_CLONE_LIN.rulel` in lin-open

This repository is the standalone LIN copy. It is **not** Compound III, **not** a Comet / USDC market, and does **not** claim Solidity uint256 / mainnet / PriceFeed parity. It is **not** the getUtilization kernel (`clone-lin-comet-util`), **not** the presentValue/principalValue kernel (`clone-lin-comet-pv`), and **not** the accruedInterestIndices kernel (`clone-lin-comet-accrue`).

Upstream was BUSL-1.1 with Change Date 2025-12-31; the Change License is GPL-2.0-or-later (this run is 2026-10-12). This LIN kernel is an original uint64-scale implementation of the published tracking wrap, not a verbatim Solidity dump.

Pinned upstream: commit `f766f51583c23acc33b2a7824654ef2029a96804`.

Canonical goldens (uint64-scale, 128-bit product):

- `tracking_delta(1, 1, 1e6, 1e6, 0)` = **1** (USDC 6-dec identity)
- `factor_confused(1, 1, 1e6)` = **1e12** (~1e12× if FACTOR_SCALE is used instead of baseScale)
- `index_confused(1, 1, 1e6)` = **1e9** (~1e9× if BASE_INDEX_SCALE is used instead of baseScale)
- `mulfactor_as_track(1, 1, 1e6)` = **0** (`mulFactor(speed*dt, baseScale)` uses /1e18)
- `tracking_delta(2, 5, 1e6, 4e6, 0)` = **2**; `reorder(2, 5, 1e6, 4e6)` = **0** (rejected `(speed*scale/total)*dt` path)
- `tracking_delta(1, 1, 1e18, 1e18, 0)` = **1** and `factor_confused(1, 1, 1e18)` = **1** (WETH 18-dec hides the FACTOR bug)
- `tracking_delta(1, 1, 1e6, 5, 10)` = **0** (below `baseMinForRewards`)
- `1e18 * 1e18 / 1` fail-closes to **0**
