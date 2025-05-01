Penulis: [Naufal](https://x.com/0xfal)

> [!NOTE]
> **WHAT IS Aztec?**\
> TBD.

# Tutorial Aztec Sequencer

## 1. Prerequisites

- [Cara terhubung ke VPS](https://github.com/ZuperHunt/Connect-to-VPS)
- [Cara menginstal Docker](https://github.com/zupercollective/Installing-Docker)

## 2. Requirements

### Hardware

| Part | Minimum | Recommended |
| ------------- | ------------- | ------------- |
| CPU | - | ≥ 8 cores |
| RAM | - | 16 GB |
| SSD | - | 1 TB |

### Software

| ✅ Linux | ✅ macOS | ✅ Windows (WSL) |
| ------------- | ------------- | ------------- |

> [!NOTE]
> Tutorial ini dibuat menggunakan Linux (Ubuntu), untuk sistem operasi lainnya mungkin akan sedikit berbeda!

## 3. Execution

### 3.1 Create a Session

Ubah `<SESSION_NAME>` menjadi terserahmu.

```
tmux new -s <SESSION_NAME>
```

### 3.2 Install Aztec Sandbox

```
bash -i <(curl -s https://install.aztec.network)
```

Ketik saja `y` pas muncul seperti gambar berikut.

![image](https://github.com/user-attachments/assets/147ccf0f-0f4b-4655-b526-9bc764229ebb)

### 3.3 Add to PATH

```
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### 3.4 Start Your Sequencer

- Ubah `<SEPOLIA_RPC>` menjadi URL RPC yang disediakan [public](https://chainlist.org) (search: **Ethereum Sepolia**) atau buat sendiri di [Alchemy](https://dashboard.alchemy.com/chains/eth?network=ETH_SEPOLIA) / [Infura](https://www.infura.io) / penyedia RPC lainnya terserahmu bebas.
- Ubah `<CONSENSUS-HOST-URL>` menjadi **Ethereum Beacon Chain Sepolia**, buat di [dRPC](https://drpc.org/login).
- Ubah `<0xYourPrivateKey>` menjadi private key wallet yang sudah diisi SepoliaETH (gunakan wallet baru, dan jangan lupa **pakai prefix 0x**).
- Ubah `<0xYourAddress>` menjadi wallet address dari private key sebelumnya.
- Ubah `<YOUR_IP_ADDRESS>` menjadi alamat IP VPS mu.

```
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls <SEPOLIA_RPC> \
  --l1-consensus-host-urls <CONSENSUS-HOST-URL> \
  --sequencer.validatorPrivateKey <0xYourPrivateKey> \
  --sequencer.coinbase <0xYourAddress> \
  --p2p.p2pIp <YOUR_IP_ADDRESS>
```

---

Reach us if you have any question:\
ZuperCollective's [Discord server](https://discord.gg/ZuperCollective) | [X(Twitter)](https://twitter.com/ZuperCollective)

# Acknowledgements

* [Aztec Docs](https://docs.aztec.network/next/the_aztec_network/guides/run_nodes)
