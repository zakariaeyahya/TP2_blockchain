# 🧱 TP N°2 – Création d’une Blockchain Privée Ethereum avec Geth

> Université Abdelmalek Essaâdi – École Nationale des Sciences Appliquées de Tétouan  
> Filière : Sciences des Données, Big Data & Intelligence Artificielle  
> Module : Fondamentaux de la Blockchain (M356)  
> Enseignant : **Pr. Imad Sassi**  
> Année universitaire : **2025–2026**

---

## 🎯 Objectif du TP

L’objectif de ce TP est de **créer un réseau Ethereum privé** à l’aide du client **Geth (Go Ethereum)** afin de comprendre le fonctionnement interne d’une blockchain :

- Créer et configurer un **bloc Genesis** personnalisé  
- Initialiser plusieurs **nœuds Ethereum** connectés entre eux  
- Créer des **comptes** et transférer des **Ethers fictifs**  
- Observer la **connexion P2P** entre les nœuds  
- Expérimenter la **structure d’un réseau Ethereum privé**

---

## ⚙️ Environnement de travail

| Élément | Détails |
|----------|----------|
| **Système** | Windows 10 (PowerShell) |
| **Client Ethereum** | Geth v1.16.7 (stable) |
| **Langage utilisé** | JavaScript (console Geth) |
| **Consensus** | Proof of Stake (PoS) |
| **Dossier du projet** | `D:\bureau\BD_AI1\ci3\blockchian\tp2` |
| **Network ID** | 2025 |
| **Ports utilisés** | 30303 (nœud 1) / 30304 (nœud 2) |

---

## 🧩 Étapes de réalisation

### 1️⃣ Installation de Geth
Téléchargement et installation depuis [geth.ethereum.org/downloads](https://geth.ethereum.org/downloads).

Vérification :
```powershell
geth version
````

---

### 2️⃣ Création du bloc Genesis

Fichier `genesis.json` :

```json
{
  "config": {
    "chainId": 2025,
    "homesteadBlock": 0,
    "byzantiumBlock": 0,
    "londonBlock": 0,
    "mergeNetsplitBlock": 0,
    "terminalTotalDifficulty": 0
  },
  "difficulty": "1",
  "gasLimit": "2100000",
  "alloc": {
    "DD15f34fe37C13Bd42A41c00e5729865C688BaDA": { "balance": "0x2000000000000000000000000" },
    "Ac526A1c14FEaACd1C6925Ed6df8c5457a4E6adB": { "balance": "0x1000000000000000000000000" }
  }
}
```

Le champ `alloc` permet de préallouer des Ethers fictifs aux comptes du réseau privé.

---

### 3️⃣ Initialisation des nœuds

```powershell
geth --datadir node1 init genesis.json
geth --datadir node2 init genesis.json
```

Résultat attendu :

```
Successfully wrote genesis state
```

---

### 4️⃣ Création des comptes

```powershell
geth --datadir node1 account new
geth --datadir node2 account new
```

* **Node 1 :** `0xDD15f34fe37C13Bd42A41c00e5729865C688BaDA`
* **Node 2 :** `0xAc526A1c14FEaACd1C6925Ed6df8c5457a4E6adB`

---

### 5️⃣ Lancement des nœuds

**Node 1**

```powershell
& "C:\Program Files\Geth\geth.exe" --datadir node1 --networkid 2025 --port 30303 --http --http.addr "127.0.0.1" --http.port 8545 --http.api "eth,net,web3,admin,miner,txpool,personal" --authrpc.port 8551 --nat "none" --nodiscover --unlock "0xDD15f34fe37C13Bd42A41c00e5729865C688BaDA" --password .\node1\pw.txt console
```

**Node 2**

```powershell
& "C:\Program Files\Geth\geth.exe" --datadir node2 --networkid 2025 --port 30304 --http.port 8546 --ipcpath "\\.\pipe\geth2.ipc" --authrpc.port 8552 --nat "none" --nodiscover console
```

---

### 6️⃣ Connexion P2P entre les nœuds

Depuis la console du **node 2** :

```js
admin.addPeer("enode://629ce5bd98...@127.0.0.1:30303")
admin.peers
```

Résultat attendu :

```json
[{
  "caps": ["eth/68", "eth/69", "snap/1"],
  "network": {
    "localAddress": "127.0.0.1:58999",
    "remoteAddress": "127.0.0.1:30303"
  }
}]
```

✅ Les deux nœuds sont connectés.

---

### 7️⃣ Vérification des soldes

```js
eth.getBalance(eth.accounts[0])
```

| Nœud   | Adresse       | Solde (wei)   | Approx. ETH |
| ------ | ------------- | ------------- | ----------- |
| Node 1 | 0xDD15f34f... | 1.5845 × 10²⁹ | ~1584 ETH   |
| Node 2 | 0xAc526A1c... | 7.9228 × 10²⁸ | ~792 ETH    |

---

### 8️⃣ Transaction test (locale)

```js
eth.sendTransaction({
  from: "0xDD15f34fe37C13Bd42A41c00e5729865C688BaDA",
  to: "0xAc526A1c14FEaACd1C6925Ed6df8c5457a4E6adB",
  value: web3.toWei(1, "ether")
})
```

Transaction enregistrée dans le *pool* local :

```js
txpool.status
txpool.inspect.pending
```

---

## 📊 Résultats et observations

* Le réseau Ethereum privé fonctionne localement via deux nœuds Geth.
* Chaque nœud possède un compte et un solde initial défini dans le `genesis.json`.
* Les nœuds communiquent via le protocole **eth/69** (post-Merge / PoS).
* Les transactions locales peuvent être simulées sans passer par un réseau public.

---

## ✅ Conclusion

Ce TP a permis de :

* comprendre la configuration d’une **blockchain Ethereum privée** ;
* manipuler les **comptes, blocs, transactions** et les **fichiers de configuration Genesis** ;
* observer la **connexion P2P** entre nœuds et la gestion des soldes.

> Résultat final : réseau privé Ethereum opérationnel et interconnecté ✅

---

## 📦 Structure du dépôt

```
tp2/
├── genesis.json
├── node1/
│   ├── keystore/
│   └── pw.txt
├── node2/
│   └── keystore/
├── README.md
```

---

## 🚀 Exécution rapide (rappel)

```bash
# Initialisation
geth --datadir node1 init genesis.json
geth --datadir node2 init genesis.json

# Lancement des nœuds
geth --datadir node1 --networkid 2025 --port 30303 console
geth --datadir node2 --networkid 2025 --port 30304 console
```

---

## 🧩 Auteur

**Nom :** YAHYA ZAKARIAE 
**Filière :** Big Data & Intelligence Artificielle
**École :** ENSA Tétouan
**Année :** 2025–2026

```

---

Souhaites-tu que je te crée aussi une **version en PDF** directement exportable à partir de ce README (avec page de garde et mise en forme universitaire) ?
```
