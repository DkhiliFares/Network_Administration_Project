
# 📡 Supervision Réseau avec Zabbix – Projet EVE-NG

Ce projet met en œuvre la **surveillance des performances réseau** et des équipements via **Zabbix** dans une topologie simulée sous **EVE-NG**.  
La supervision couvre plusieurs VLANs répartis entre deux départements, avec des switches, des VM Ubuntu et un serveur Zabbix.

---
## 🧠 À propos de Zabbix

**Zabbix** est une solution open-source de supervision permettant de collecter, stocker et analyser les données de performance et de disponibilité. Elle utilise différents moyens (Agent, SNMP, IPMI, etc.) pour superviser les machines, routeurs, switches et applications.

---

## 🧱 Architecture réseau

### VLANs et sous-réseaux :
| Département | VLAN | Sous-réseau |
|------------|------|-------------|
| Core       | -    | 192.168.10.0/24, 192.168.11.0/24, 192.168.12.0/24, 192.168.13.0/24 |
| Dep-Math   | 20   | 192.168.20.0/24, 192.168.21.0/24 |
| Dep-Info   | 20   | 192.168.30.0/24, 192.168.31.0/24 |

### Équipements :
| Nom          | Rôle               | Interface de management | OS / Plateforme        |
|---------------|---------------------|--------------------------|-------------------------|
| Ubuntu_SRV    | Serveur Zabbix      | 192.168.10.X             | Ubuntu Server           |
| Ubuntu Desktop| Client Web          | 192.168.10.Y             | Ubuntu Desktop          |
| R1            | Routeur principal   | 192.168.11.X             | Cisco Router (IOL)      |
| SW1           | Switch Core         | 192.168.10-13.X          | Cisco Switch (IOL)      |
| SW2           | Switch Département Dep-Math | 192.168.12.X     | Cisco Switch (IOL)      |
| SW3           | Switch Département Dep-Info | 192.168.13.X     | Cisco Switch (IOL)      |
| SW21, SW22    | Access switches Dep-Math | -                  | Cisco Switch            |
| SW31, SW32    | Access switches Dep-Info | -                  | Cisco Switch            |


## ⚙️ Étapes d'installation de Zabbix

### 🔹 1. Mise à jour du système (Ubuntu_SRV)
```bash
sudo apt update && sudo apt upgrade -y
```

### 🔹 2. Ajouter le dépôt Zabbix
```bash
wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo apt update
```

### 🔹 3. Installer les paquets nécessaires
```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf \
zabbix-sql-scripts zabbix-agent mysql-server -y
```

### 🔹 4. Configuration de MySQL
```bash
sudo mysql -uroot -p
```
Puis dans le shell MySQL :
```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'zabbixpass';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 🔹 5. Importer le schéma SQL
```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -pzabbixpass zabbix
```

### 🔹 6. Modifier la configuration du serveur Zabbix
```bash
sudo nano /etc/zabbix/zabbix_server.conf
```
Modifier :
```
DBPassword=zabbixpass
```

### 🔹 7. Démarrer les services
```bash
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

---

## 🌐 Accès à l’interface Web

1. Depuis Ubuntu Desktop, ouvrir un navigateur.
2. Accéder à `http://192.168.10.X/zabbix`
3. Suivre l’assistant :
   - DB: `zabbix`
   - Utilisateur: `zabbix`
   - Mot de passe: `zabbixpass`

---

## 📡 Supervision des équipements

### ✅ Équipements Cisco (SW1, SW2, SW3)

#### Activer SNMP :
```bash
conf t
snmp-server community public RO
end
wr
```

#### Dans l'interface Zabbix :
- Créer un hôte (ex: SW2)
- IP : Adresse de management
- Interface : SNMP
- Template : `Template Net Cisco SNMPv2`

---

### ✅ Ubuntu Desktop ou autres VM Linux

#### Installer l’agent Zabbix :
```bash
sudo apt install zabbix-agent -y
sudo nano /etc/zabbix/zabbix_agentd.conf
```
Modifier :
```
Server=192.168.10.X
```
Puis :
```bash
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent
```

#### Dans l'interface Zabbix :
- Interface : Agent
- Template : `Template OS Linux by Zabbix agent`

---

## 📊 Configuration avancée

- **Triggers** : Ex. CPU > 80%, Ping down, etc.
- **Graphiques** : Bande passante, latence, uptime
- **Actions** : Envoi d’emails, notifications

---

## ✅ Résultat attendu

- Supervision en temps réel des équipements réseau
- Alertes en cas d’incidents
- Visualisation centralisée de la santé du réseau

---



## 📂 Crédits

- Projet réalisé sous [EVE-NG](https://www.eve-ng.net/)
- Outils : Ubuntu Server/Desktop, Cisco IOL, Zabbix
```

