# Mode Opératoire - Administration DHCP & Web

> **Auteur :** Edib Saoud
> **Date :** 02/2025 - 03/2025
> **Version :** 1.0
> **Cible :** Support Informatique Niveau 1 / SISR

---

## 1. Gestion du Service Kea DHCP

**Cas d'usage :** Les clients n'obtiennent plus d'adresse IP (le réseau est bloqué).

### Vérifier l'état du serveur DHCP
```bash
sudo systemctl status kea-dhcp4-server
```
Si le service est indiqué comme `failed` (en rouge) :
1. Vérifier la syntaxe du fichier JSON (souvent un problème de virgule manquante) :
   `nano /etc/kea/kea-dhcp4.conf`
2. Redémarrer le service :
   `sudo systemctl restart kea-dhcp4-server`

### Lire les journaux DHCP (Baux attribués et erreurs)
Pour savoir si une machine a bien reçu son adresse IP ou pourquoi le service a planté :
```bash
# Consulter les logs spécifiques de Kea
sudo tail -f /var/log/kea-dhcp4.log

# Consulter les logs système globaux
sudo journalctl -u kea-dhcp4-server -e
```

---

## 2. Gestion du Service Web Apache2

**Cas d'usage :** L'intranet affiche "Ce site est inaccessible" ou une erreur 500.

### Vérifier l'état d'Apache
```bash
sudo systemctl status apache2
```

### Vérifier la syntaxe des fichiers de configuration Apache
Avant tout redémarrage, si un VirtualHost a été modifié :
```bash
sudo apache2ctl configtest
```
*(Le retour doit être `Syntax OK`)*.

### Consulter les journaux d'erreurs (Access logs / Error logs)
Si la page ne charge pas ou manque de ressources (CSS, images) :
```bash
# Pour voir les erreurs (ex: fichier introuvable)
sudo tail -f /var/log/apache2/error.log

# Pour voir qui se connecte en temps réel
sudo tail -f /var/log/apache2/access.log
```

---

## 3. Modification de l'Intranet

**Cas d'usage :** Mettre à jour la page d'accueil de l'école.

1. Se connecter en SSH au serveur Linux.
2. Naviguer dans le dossier du site :
   ```bash
   cd /var/www/intranet.iris.local/public_html
   ```
3. Éditer ou remplacer le fichier `index.html` :
   ```bash
   nano index.html
   ```
4. Sauvegarder les modifications. Les changements sont visibles instantanément en rafraîchissant le navigateur web côté client.
