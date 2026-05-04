# Procédure d'Installation - Services DHCP et Web

> **Auteur :** Edib Saoud
> **Date :** 02/2025 - 03/2025
> **Version :** 1.0
> **Statut :** Validé

---

## 1. Pré-requis Système

- Machine virtuelle Debian Linux 12 (CLI).
- Accès root ou privilèges `sudo`.
- Connectivité réseau configurée en IP Statique.

### Configuration réseau de base
Éditer le fichier `/etc/network/interfaces` pour attribuer l'IP fixe :
```text
auto enp0s3
iface enp0s3 inet static
    address 192.168.20.10
    netmask 255.255.255.0
    gateway 192.168.20.254
```
Appliquer les paramètres : `systemctl restart networking`.

---

## 2. Déploiement de Kea DHCP

### 2.1 Installation
```bash
sudo apt update
sudo apt install kea-dhcp4-server
```

### 2.2 Configuration du service (JSON)
Ouvrir le fichier de configuration principal :
```bash
sudo nano /etc/kea/kea-dhcp4.conf
```
Adapter le fichier JSON avec notre architecture :
```json
{
  "Dhcp4": {
    "interfaces-config": {
      "interfaces": ["enp0s3"]
    },
    "valid-lifetime": 43200,
    "subnet4": [
      {
        "subnet": "192.168.20.0/24",
        "pools": [ { "pool": "192.168.20.100 - 192.168.20.200" } ],
        "option-data": [
          { "name": "routers", "data": "192.168.20.254" },
          { "name": "domain-name-servers", "data": "8.8.8.8, 8.8.4.4" }
        ],
        "reservations": [
          {
            "hw-address": "00:11:22:33:44:55",
            "ip-address": "192.168.20.50",
            "hostname": "Imprimante_Salle_Profs"
          }
        ]
      }
    ]
  }
}
```

### 2.3 Activation
Vérifier la syntaxe et redémarrer le service :
```bash
sudo systemctl enable kea-dhcp4-server
sudo systemctl restart kea-dhcp4-server
```

---

## 3. Déploiement du Service Web (Apache2)

### 3.1 Installation
```bash
sudo apt install apache2 -y
```

### 3.2 Création de l'Intranet
Créer le répertoire web et la page HTML d'accueil :
```bash
sudo mkdir -p /var/www/intranet.iris.local/public_html
sudo chown -R $USER:$USER /var/www/intranet.iris.local/public_html
sudo chmod -R 755 /var/www/intranet.iris.local
```
Créer un fichier `index.html` :
```bash
nano /var/www/intranet.iris.local/public_html/index.html
```
*(Y insérer le code HTML de bienvenue pour l'intranet IRIS Mediaschool).*

### 3.3 Configuration du VirtualHost
```bash
sudo nano /etc/apache2/sites-available/intranet.iris.local.conf
```
Ajouter la configuration :
```apache
<VirtualHost *:80>
    ServerAdmin admin@iris.local
    ServerName intranet.iris.local
    DocumentRoot /var/www/intranet.iris.local/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Activer le site et recharger Apache :
```bash
sudo a2ensite intranet.iris.local.conf
sudo a2dissite 000-default.conf
sudo systemctl reload apache2
```

*(Note : Pour accéder via `http://intranet.iris.local`, les clients doivent ajouter l'IP `192.168.20.10` dans leur serveur DNS interne ou leur fichier `hosts`).*
