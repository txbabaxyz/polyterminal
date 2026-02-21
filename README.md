# Polymarket Crypto 15-Minute Trading Bot

Trading terminal for Polymarket 15-minute crypto markets (BTC, ETH, SOL, XRP).

## Features

- **Live Trading** — Trade UP/DOWN positions in real-time
- **Multi-cryptocurrency** — Support for BTC, ETH, SOL, XRP
- **WebSocket Subscriptions** — Real-time price data from Polymarket
- **Telegram Notifications** — Instant trade alerts
- **Auto-redeem** — Automatic winnings collection after market close
- **Session P/L** — Track profit/loss per session
- **CLI Interface** — Convenient terminal-based control

## Requirements

- Python 3.10 or higher
- Polygon (MATIC) wallet with USDC
- Small amount of POL/MATIC for gas fees
- VPN (if needed to bypass geo-restrictions)

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/txbabaxyz/polyterminal.git
cd polyterminal
```

### 2. Create Virtual Environment

**IMPORTANT: You must use a virtual environment (venv)!**

```bash
# Create venv
python3 -m venv venv

# Activate venv
# Linux/macOS:
source venv/bin/activate

# Windows:
.\venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configuration

```bash
# Copy the configuration file
cp .env.example .env

# Open .env and fill in your data
nano .env  # or any text editor
```

## Configuration (.env)

```env
# ===========================================
# REQUIRED PARAMETERS
# ===========================================

# Your Polygon wallet private key (with 0x prefix)
PRIVATE_KEY=0xyourprivatekey

# Signature type:
# 0 = EOA (standard wallet created by you)
# 1 = POLY_PROXY (registered via Magic Link / Email on Polymarket)
# 2 = POLY_GNOSIS_SAFE (registered via MetaMask / Phantom on Polymarket)
SIGNATURE_TYPE=0

# Proxy wallet address (only for SIGNATURE_TYPE 1 or 2)
# Leave empty for type 0
FUNDER_ADDRESS=

# ===========================================
# API CREDENTIALS (optional)
# ===========================================
# Auto-generated, but can be specified for reuse
POLY_API_KEY=
POLY_API_SECRET=
POLY_API_PASSPHRASE=

# ===========================================
# TRADING SETTINGS
# ===========================================

# Position size in contracts (default 10)
CONTRACTS_SIZE=10

# ===========================================
# TELEGRAM NOTIFICATIONS (optional)
# ===========================================

# Bot token (get from @BotFather)
TELEGRAM_BOT_TOKEN=

# Your Chat ID (get from @userinfobot)
TELEGRAM_CHAT_ID=

# ===========================================
# POLYGON RPC (recommended)
# ===========================================

# Private RPC for stable operation
# Free options:
# - Alchemy: https://polygon-mainnet.g.alchemy.com/v2/YOUR_API_KEY
# - Ankr: https://rpc.ankr.com/polygon
# - Public: https://polygon-rpc.com
RPC_URL=https://polygon-rpc.com
```

### How to Determine SIGNATURE_TYPE

| Type | When to Use |
|------|-------------|
| 0 | You created the wallet yourself (MetaMask, Ledger, etc.) without registering on Polymarket |
| 1 | You registered on Polymarket via Magic Link / Email |
| 2 | You registered on Polymarket via MetaMask / Phantom / Browser Wallet |

For types 1 and 2, you need `FUNDER_ADDRESS` — find it at https://polymarket.com/settings

## Usage

### Main Launcher (recommended)

```bash
# Activate venv (if not already activated)
source venv/bin/activate

# Run the main menu
python3 launcher.py
```

### Launcher Menu:

```
  [1]  Live Trading      — Start trading
  [2]  Redeem All        — Collect all winnings
  [3]  Redeem Market     — Redeem from specific market
  [4]  Check Balance     — Check USDC balance
  [5]  Generate Keys     — Generate API keys
  [6]  Set Allowances    — Set permissions (first time only)
  
  [Q]  Quit
```

### Direct Trading Launch

```bash
source venv/bin/activate
python3 trade.py
```

## Trading Controls

| Key | Action |
|-----|--------|
| **1** | BUY UP (bet on price increase) |
| **2** | BUY DOWN (bet on price decrease) |
| **S** | Increase position size |
| **D** | Decrease position size |
| **F** | Toggle FOK/FAK mode |
| **R** | Refresh balance |
| **M** | Open menu (Redeem/Settings) |
| **C** | Switch cryptocurrency |
| **Q** | Quit |

## Scripts

| File | Description |
|------|-------------|
| `launcher.py` | Main menu |
| `trade.py` | Main trading script |
| `redeem.py` | Manual redeem for specific market |
| `redeemall.py` | Automatic redeem for all positions |
| `check_balance.py` | Check USDC balance and allowances |
| `set_allowances.py` | Set contract permissions |
| `generate_keys.py` | Generate API keys |
| `telegram_bot.py` | Telegram bot for remote control |
| `logger.py` | Logging module |
| `redeem_lock.py` | File lock for redeem operations |

## Initial Setup (Allowances)

Before your first trade, you must allow Polymarket contracts to use your USDC:

```bash
source venv/bin/activate
python3 set_allowances.py
```

**Required:** Small amount of POL (MATIC) for gas fees.

## Telegram Bot

For remote control, you can run the Telegram bot:

```bash
source venv/bin/activate
python3 telegram_bot.py
```

### Bot Commands:
- `/status` — System status
- `/balance` — Wallet balance
- `/redeemall` — Collect all winnings
- `/stop` — Stop trading
- `/restart` — Restart trading
- `/help` — Help

## Troubleshooting

### "invalid signature"

1. Check that `SIGNATURE_TYPE` matches your registration method
2. For types 1/2, ensure `FUNDER_ADDRESS` is correct
3. Try changing the `negRisk` flag

### "not enough balance / allowance"

```bash
python3 set_allowances.py
```

### "Rate limit exceeded"

1. Use a private RPC (Alchemy, Ankr)
2. Add to .env:
```env
RPC_URL=https://rpc.ankr.com/polygon
```

### "DNS / Connection blocked"

1. Use a VPN
2. Change DNS to 1.1.1.1 or 8.8.8.8

### Balance not updating

Check logs:
```bash
tail -50 logs/trade_*.log | grep -i balance
```

## Log Structure

Logs are saved in the `logs/` folder with 3-hour rotation:

```
logs/
├── trade_2024-01-15_09h.log
├── trade_2024-01-15_12h.log
├── redeem_2024-01-15_09h.log
└── ...
```

## Important Notes

1. **USDC Type**: Polymarket uses **USDC (Bridged)**, not USDC.e (Native). If you have USDC.e — swap it on QuickSwap or 1inch.

2. **Rate limits**: Public RPCs have limitations. For stable operation, use private RPCs (Alchemy, Ankr).

3. **Gas fees**: Keep a small POL/MATIC balance for transaction fees.

4. **Risks**: Cryptocurrency trading involves risks. Only use funds you can afford to lose.

## Updating

```bash
cd polyterminal
git pull
source venv/bin/activate
pip install -r requirements.txt --upgrade
```

## License

MIT License

## Support

If you encounter issues, create an Issue in the repository.
