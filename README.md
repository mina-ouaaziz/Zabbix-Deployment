# 📊 Zabbix 7.0 — Déploiement et monitoring d'infrastructure

> Déploiement complet de Zabbix 7.0 sur Ubuntu Server 24.04 en environnement virtualisé —
> installation from scratch, configuration du monitoring, surveillance en temps réel et alertes automatiques.

---

## 🛠️ Stack technique

| Composant | Version |
|-----------|---------|
| OS | Ubuntu Server 24.04 |
| Virtualisation | Oracle VirtualBox 7.2.6 |
| Monitoring | Zabbix 7.0.24 |
| Serveur web | Apache 2.4 |
| Base de données | MySQL 8.0 |
| Langage | PHP 8.3 |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────┐
│           labreseau (interne)           │
│            192.168.1.0/24               │
└──────────────────┬──────────────────────┘
                   │
       ┌───────────┴───────────┐
       │                       │
┌──────▼──────┐         ┌──────▼──────┐
│ SRV-ZABBIX  │         │  SRV-LINUX  │
│ 192.168.1.40│◄────────│ 192.168.1.30│
│ Zabbix 7.0  │ Agent   │ Zabbix Agent│
└─────────────┘         └─────────────┘
```

---

## ⚙️ Installation

### 1. Ajout du dépôt Zabbix
```bash
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-2+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_7.0-2+ubuntu24.04_all.deb
sudo apt update
```

### 2. Installation des composants
```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf \
zabbix-sql-scripts zabbix-agent default-mysql-server -y
```

### 3. Configuration de la base de données
```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'MotDePasse';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
```

### 4. Import du schéma
```bash
sudo mysql -u root -e "SET GLOBAL log_bin_trust_function_creators = 1;"
sudo zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | \
mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

### 5. Configuration et démarrage
```bash
sudo sed -i 's/# DBPassword=/DBPassword=MotDePasse/' /etc/zabbix/zabbix_server.conf
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

<img width="1042" height="547" alt="Image" src="https://github.com/user-attachments/assets/3a5daacd-670e-4ffd-a28c-e276f60c0905" />
---

## 🔐 Sécurisation post-installation

- ✅ Changement du mot de passe Admin par défaut
- ✅ Configuration du pare-feu (port 10050 agent, port 80 web)
- ✅ Réseau interne dédié pour la communication agent/serveur

<img width="1488" height="905" alt="Image" src="https://github.com/user-attachments/assets/9948a2bc-25be-4413-98a2-f60a8cbb1e49" />
---

## ✅ Configuration réalisée

### 🖥️ Hôtes monitorés

| Hôte | IP | Modèle | Statut |
|------|----|--------|--------|
| Zabbix server | 127.0.0.1 | Linux by Zabbix agent + Zabbix server health | ✅ Actif |
| SRV-LINUX | 192.168.1.30 | Linux by Zabbix agent | ✅ Actif |

<img width="1490" height="905" alt="Image" src="https://github.com/user-attachments/assets/11460615-b4ed-43ce-bdc5-c047504facc6" />

### 📈 Dashboard Monitoring Lab

Widgets configurés :
- 📊 Graphique **CPU utilization** — SRV-LINUX en temps réel
- 📊 Graphique **Memory utilization** — SRV-LINUX en temps réel
- 🟢 **Statut des hôtes** — disponibilité en temps réel
- 🔔 **Alertes actives** — problèmes en cours
  
  <img width="1919" height="1034" alt="Image" src="https://github.com/user-attachments/assets/34ea3537-7dea-4da3-afda-bb7b86d03187" />

### 🔔 Alertes configurées

<img width="1408" height="918" alt="Image" src="https://github.com/user-attachments/assets/f48a7264-12bd-4ee6-a09f-38e7f5b9f54e" />
<img width="1410" height="915" alt="Image" src="https://github.com/user-attachments/assets/1c6309df-6ab4-47e9-b480-4d0d869e52fe" />

| Nom | Condition | Opération | État |
|-----|-----------|-----------|------|
| Hôte inaccessible | ICMP ping KO | Notification Admin | ✅ Activé |

<img width="1475" height="917" alt="Image" src="https://github.com/user-attachments/assets/64de682f-adf5-4ace-a231-6c2815694489" />
---

## 🔧 Difficultés rencontrées et résolutions

### 1. Soft lockups CPU sur VirtualBox
- **Symptôme** : kernel gelé, VM inaccessible
- **Résolution** : passage de l'interface de paravirtualisation en **KVM**

### 2. Erreur d'import du schéma SQL
- **Symptôme** : `You do not have the SUPER privilege and binary logging is enabled`
- **Résolution** : `SET GLOBAL log_bin_trust_function_creators = 1`

### 3. Impossible de se connecter à la base de données
- **Symptôme** : `Access denied for user 'zabbix'@'localhost'`
- **Résolution** : recréation de l'utilisateur avec `mysql_native_password` + utilisation de `127.0.0.1` au lieu de `localhost`

### 4. Agent Zabbix inaccessible depuis le serveur
- **Symptôme** : hôte SRV-LINUX en rouge dans Zabbix
- **Résolution** : configuration du réseau interne **labreseau** sur les deux VMs + mise à jour de l'IP dans la config agent (`192.168.1.30` → `192.168.1.40`)

---

## 📊 Compétences démontrées

`Linux` `Zabbix` `Monitoring` `MySQL` `Apache` `PHP` `Administration système` `Virtualisation` `Réseau` `Alerting` `Infrastructure` `ITIL`

---

## Auteure

**Mina OUAAZIZ** — Technicienne Supérieure Systèmes et Réseaux  
Passionnée par la cybersécurité défensive, l'administration système et le support IT.
