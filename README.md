Penulis: [Naufal](https://x.com/0xfal)

> [!NOTE]
> **WHAT IS Aztec?**\
> Building the first privacy network on Ethereum.

# Tutorial Aztec Sequencer

## 1. Requirements

### Hardware

| Part | Minimum | Recommended |
| ------------- | ------------- | ------------- |
| CPU | 4 cores | 8 cores |
| RAM | 4 GB | 16 GB |
| SSD | 80 GB | 1 TB |

### Software

| ✅ Linux | ✅ macOS | ✅ Windows (WSL) |
| ------------- | ------------- | ------------- |

> [!NOTE]
> Tutorial ini dibuat menggunakan Linux (Ubuntu 24 LTS), untuk sistem operasi lainnya mungkin akan sedikit berbeda !!

> [!WARNING]
> Penulis mendapati adanya kasus Ubuntu 22 LTS yang bermasalah: **file /root/.Xauthority doest not exist**\
> Sebaiknya gunakan versi Ubuntu yang sama dengan penulis!

## 2. Prerequisites

- [Cara terhubung ke VPS](https://github.com/ZuperHunt/Connect-to-VPS)
- [Cara menginstal Docker](https://github.com/zupercollective/Installing-Docker)

## 3. Execution

### 3.1 Install Aztec Sandbox

```
bash -i <(curl -s https://install.aztec.network)
```

Ketik saja `y` pas muncul seperti gambar berikut.

![image](https://github.com/user-attachments/assets/147ccf0f-0f4b-4655-b526-9bc764229ebb)

### 3.2 Add to PATH

```
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Atau kalau di atas tidak bisa, coba opsi berikut.

```
echo "export PATH="$PATH:/home/aztec/.aztec/bin"" | tee -a .bash_profile
source ~/.bashrc
```

### 3.3 Update Aztec Toolchain

```
aztec-up alpha-testnet
```

### 3.4 Create a Session

Ubah `<SESSION_NAME>` menjadi terserahmu.

```
tmux new -s <SESSION_NAME>
```

### 3.5 Start Your Sequencer

- Ubah `<EXECUTION_CLIENT>` menjadi **Ethereum Sepolia RPC URL**:
  - [Alchemy](https://dashboard.alchemy.com/chains/eth?network=ETH_SEPOLIA) (goat)
  - [Infura/MetaMask Developer](https://developer.metamask.io/key/all-endpoints) (bapuk)
  - [QuickNode](https://dashboard.quicknode.com/endpoints/new/ETH/ethereum-sepolia) (gud)
  - Penyedia RPC lainnya terserahmu bebas.

- Ubah `<CONSENSUS_CLIENT>` menjadi **Ethereum Beacon Chain Sepolia RPC URL**:
  - [dRPC](https://drpc.org/login)
  
    ![image](https://github.com/user-attachments/assets/13c0d6b3-d244-4e66-9566-e78bad959327)
  
  - [Chainstack](https://console.chainstack.com/nodes)
  
    ![image](https://github.com/user-attachments/assets/89b297f6-7618-4663-9f28-4d147b5bd66e)
    ![image](https://github.com/user-attachments/assets/82d26203-bab0-4b30-859b-a648cbc280c4)

- Ubah `<0xYourPrivateKey>` menjadi private key wallet yang sudah diisi minimal 0.05SepoliaETH (gunakan wallet baru, dan jangan lupa **pakai prefix 0x**).
- Ubah `<0xYourAddress>` menjadi wallet address dari private key sebelumnya.
- Ubah `<YOUR_IP_ADDRESS>` menjadi alamat IP VPS mu.

> [!WARNING]
> SEBELUM MENJALANKAN PERINTAH, ikuti instruksi di atas !!!

```
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls <EXECUTION_CLIENT> \
  --l1-consensus-host-urls <CONSENSUS_CLIENT> \
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

Kalau fully synced seperti ini:

![image](https://github.com/user-attachments/assets/b3441eee-9318-4434-b9d6-3384a2f76738)

### 3.6 Get Your Block Number

Detach dulu dari session, `ctrl` + `b`, kemudian tekan tombol `d`. Kalau kalian dari smartphone, simply exit dulu trus connect lagi, nanti pasti akan mulai dari luar session.

> [!WARNING]
> SEBELUM MENJALANKAN PERINTAH, ganti `<YOUR_IP_ADDRESS>` menjadi alamat IP VPS mu !!!

Kalau sudah berada di luar session, jalankan perintah berikut.

```
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
<YOUR_IP_ADDRESS>:8080 | jq -r ".result.proven.number"
```

Akan muncul `block number` mu, copy dan simpan.

### 3.7 Get Your Proof

> [!WARNING]
> SEBELUM MENJALANKAN PERINTAH, ganti `<YOUR_IP_ADDRESS>` menjadi alamat IP VPS mu, dan ganti `<BLOCK_NUMBER>` dengan block number yang didapat sebelumnya !!!

Jalankan perintah berikut.

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

- Ubah `<EXECUTION_CLIENT>` menjadi RPC URL mu sebelumnya.
- Ubah `<0xYourPrivateKey>` menjadi private key wallet mu sebelumnya.
- Ubah `<0xYourAddress>` menjadi wallet address mu sebelumnya.

> [!WARNING]
> SEBELUM MENJALANKAN PERINTAH, ikuti instruksi di atas !!!

```
aztec add-l1-validator \
  --l1-rpc-urls <EXECUTION_CLIENT> \
  --private-key <0xYourPrivateKey> \
  --attester <0xYourAddress> \
  --proposer-eoa <0xYourAddress> \
  --staking-asset-handler 0xF739D03e98e23A7B65940848aBA8921fF3bAc4b2 \
  --l1-chain-id 11155111
```

> [!NOTE]
> You may see a warning when trying to register as a validator. To maintain network health there is a daily quota for validators to join the validator set. If you are not able to join, it could mean that today's quota of validators has already been added to the set. If you see this, you can try again later.

---

# QnA

## Private key with or without 0x?

Gunakan saja 0x, itu dari official guide Aztec. Kalau pas kalian ekspor private key dari wallet tertentu tapi yang keluar tidak ada 0x-nya, tinggal tambahin 0x di depannya. 

## How to attach to my session?

```
tmux a -t <SESSION_NAME>
```

## How to update my node?

Kembali ke session, matikan node mu dengan cara `ctrl` + `c`, dan jalankan perintah berikut.

```
aztec-up alpha-testnet
```

Hidupkan kembali node mu, seperti step 3.5.

---

Reach us if you have any question:\
ZuperCollective's [Discord server](https://discord.gg/ZuperCollective) | [X(Twitter)](https://twitter.com/ZuperCollective)

# Acknowledgements

* [Aztec Docs](https://docs.aztec.network/next/the_aztec_network/guides/run_nodes)
* [Tmux Cheat Sheet & Quick Reference](https://tmuxcheatsheet.com/)
