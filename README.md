# Solana examples

Credential-free host programs for the Solana Gadget Kit
(`github.com/themusebook/solana-gadget-kit`). They do not flash a board, open
a network connection, or read a provider key.

The ESP32 starting point is the Muse round-LCD gadget SDK. These programs are
the part you can run on a computer:

| Program | What it does |
|---|---|
| `hardware_wallet` | On-device wallet. Refuses to export the secret, shows the 240×240 review line, and signs only after a physical confirm. |
| `lcd_status` | Drives the 240×240 surface through home, review summary, expired, and cancelled. |
| `robotics_gate` | Applies `servo:15` only for a matching Solana confirmation. Mismatch, expiry, cancel, and a missing confirmation are refused. |
| `solana_os_check` | Checks the Solana OS 0.4.4 bundle in `firmware/solana-os/0.4.4` against `SHA256SUMS`. |
| `portfolio_preview` | Renders a fixed SOL/USDC quote preview. 10000000 lamports, 2500000 USDC base units, minimum out 980000. |

Pocket Wallet in `../pocket-wallet` is a different wallet type. Its physical
press moves the screen to phone review and cannot sign or submit.

## Build and run

From the repository root:

```sh
make -C solana test examples
./solana/build/hardware_wallet
./solana/build/robotics_gate
./solana/build/solana_os_check
./solana/build/lcd_status
./solana/build/portfolio_preview
```

`make -C solana pocket-firmware` also runs the vendored Pocket Wallet
confirmation tests. The public demonstration seed is the 32-byte string
`solana-gadget-kit-hw-wallet-seed`. It is not a funded key. Ed25519 signatures
for that seed and the same payload match across runs.

Primary lines:

```text
WALLET signature=<128 hex chars> confirmed_verify=ok unconfirmed=refused
ROBOTICS match=applied mismatch=refused expired=refused cancelled=refused missing=refused
SOLANA_OS 0.4.4 checksums=ok files=4
PORTFOLIO sol=0.010000000 usdc=2.500000 quote_min_out=0.980000 venue=fixture
LCD frames=home,review,expired,cancelled size=240x240
```

The hardware-wallet signature is deterministic. A different payload does not
verify. The robotics actuator records `servo:15` only for command `arm.nudge`
while the confirmation is still inside its window.
