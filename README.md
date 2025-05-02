Penulis: [Naufal](https://x.com/0xfal)

> [!NOTE]
> **WHAT IS Aztec?**\
> Building the first privacy network on Ethereum.

# Tutorial Aztec Sequencer

## 1. Requirements

### Hardware

| Part | Minimum | Recommended |
| ------------- | ------------- | ------------- |
| CPU | 2 cores | 8 cores |
| RAM | 2 GB | 16 GB |
| SSD | 20 GB | 1 TB |

### Software

| ✅ Linux | ✅ macOS | ✅ Windows (WSL) |
| ------------- | ------------- | ------------- |

> [!NOTE]
> Tutorial ini dibuat menggunakan Linux (Ubuntu), untuk sistem operasi lainnya mungkin akan sedikit berbeda!

## 2. Prerequisites

- [Cara terhubung ke VPS](https://github.com/ZuperHunt/Connect-to-VPS)
- [Cara menginstal Docker](https://github.com/zupercollective/Installing-Docker)

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

### 3.4 Update Aztec Toolchain

```
aztec-up alpha-testnet
```

### 3.5 Start Your Sequencer

- Ubah `<SEPOLIA_RPC>` menjadi URL RPC yang disediakan [public](https://chainlist.org) (search: **Ethereum Sepolia**) atau buat sendiri di [Alchemy](https://dashboard.alchemy.com/chains/eth?network=ETH_SEPOLIA) / [Infura](https://www.infura.io) / penyedia RPC lainnya terserahmu bebas.

- Ubah `<CONSENSUS-HOST-URL>` menjadi **Ethereum Beacon Chain Sepolia**, buat di [dRPC](https://drpc.org/login).
![image](https://github.com/user-attachments/assets/13c0d6b3-d244-4e66-9566-e78bad959327)

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

TUNGGU sampai node-mu synced baru lanjut ke step berikutnya, jalankan perintah berikut untuk megecek statusnya. Ganti `<YOUR_IP_ADDRESS>` menjadi alamat IP VPS mu.

```
CID=$(docker ps -q --filter ancestor=aztecprotocol/aztec)
docker exec -it "$CID" curl -s \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"node_getWorldStateSyncStatus","params":[]}' \
  <YOUR_IP_ADDRESS> | jq
```

### 3.6 Get Your Block Number

Detach dulu dari session, `ctrl` + `b`, kemudian tekan tombol `d`. Kalau kalian dari smartphone, simply exit dulu trus connect lagi, nanti pasti akan mulai dari luar session.

Kalau sudah berada di luar session, jalankan perintah berikut. Ganti `<YOUR_IP_ADDRESS>` menjadi alamat IP VPS mu.

```
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
<YOUR_IP_ADDRESS>:8080 | jq -r ".result.proven.number"
```

Akan muncul `block number` mu, copy dan simpan.

### 3.7 Get Your Proof

Jalankan perintah berikut, ganti `<YOUR_IP_ADDRESS>` menjadi alamat IP VPS mu, dan ganti `<BLOCK_NUMBER>` dengan block number yang didapat sebelumnya.

```
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getArchiveSiblingPath","params":["<BLOCK_NUMBER>","<BLOCK_NUMBER>"],"id":67}' \
<YOUR_IP_ADDRESS>:8080 | jq -r ".result"
```

Akan muncul `proof` mu, copy dan simpan.

### 3.8 Get Role

- Join [Aztec Discord server](https://discord.gg/aztec).
- Jalankan perintah `/operator start` di [operator channel](https://discord.com/channels/1144692727120937080/1367196595866828982).
- Isi dengan wallet address, block number, dan proof punyamu.

### 3.9 Register as a Validator

- Ubah `<SEPOLIA_RPC>` menjadi RPC URL mu sebelumnya.
- Ubah `<0xYourPrivateKey>` menjadi private key wallet mu sebelumnya.
- Ubah `<0xYourAddress>` menjadi wallet address mu sebelumnya.

```
aztec add-l1-validator \
  --l1-rpc-urls <SEPOLIA_RPC> \
  --private-key <0xYourPrivateKey> \
  --attester <0xYourAddress> \
  --proposer-eoa <0xYourAddress> \
  --staking-asset-handler 0xF739D03e98e23A7B65940848aBA8921fF3bAc4b2 \
  --l1-chain-id 11155111
```

Kalian akan mendapati error apabila saldo ETH dari contract Aztec sedang kosong, cek berkala di [explorer](https://sepolia.etherscan.io/address/0x6653c74c968Ee7EF8819A10B04B848Dd36610f66). Kalau ada ETH-nya baru bisa mendaftar validator.

---

Reach us if you have any question:\
ZuperCollective's [Discord server](https://discord.gg/ZuperCollective) | [X(Twitter)](https://twitter.com/ZuperCollective)

# Acknowledgements

* [Aztec Docs](https://docs.aztec.network/next/the_aztec_network/guides/run_nodes)
* [Tmux Cheat Sheet & Quick Reference](https://tmuxcheatsheet.com/)
