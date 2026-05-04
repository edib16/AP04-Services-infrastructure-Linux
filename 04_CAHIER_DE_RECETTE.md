# Cahier de Recette et Tests - Infrastructure Linux

> **Auteur :** Edib Saoud
> **Date :** 02/2025 - 03/2025
> **Version :** 1.0
> **Statut :** Validé

---

## 1. Objectifs de la Recette

L'objectif de ce document est de valider le bon fonctionnement du serveur d'infrastructure (`SRV-LINUX-01`). Il s'assure que l'automatisation réseau via DHCP est effective et que la mise à disposition de l'intranet sous Apache2 répond aux exigences du cahier des charges.

## 2. Tableau de Recette Infrastructure

| N° | Catégorie | Scénario de Test | Procédure | Résultat Attendu | Statut |
|:---|:---|:---|:---|:---|:---:|
| **T1** | **DHCP** | Attribution dynamique | Démarrer un poste client Windows (en DHCP auto). Ouvrir `cmd` et taper `ipconfig`. | Le client reçoit une adresse IP comprise entre `192.168.20.100` et `.200` avec la bonne passerelle. | ✅ Validé |
| **T2** | **DHCP** | Libération du bail | Taper `ipconfig /release` puis `ipconfig /renew`. | Le client perd son IP, puis récupère correctement une IP via le DHCP Kea. Le fichier de logs du serveur enregistre le "DHCPACK". | ✅ Validé |
| **T3** | **DHCP** | Réservation IP (MAC) | Connecter la machine fictive simulant l'imprimante avec l'adresse MAC `00:11:22:33:44:55`. | La machine obtient systématiquement l'IP `192.168.20.50`, en dehors de la plage d'adresses dynamiques. | ✅ Validé |
| **T4** | **Web** | Accès à l'intranet | Ouvrir un navigateur web sur un client et taper `http://192.168.20.10`. | La page HTML de l'intranet IRIS s'affiche correctement (titre, images, CSS s'il y en a). | ✅ Validé |
| **T5** | **Web** | Résolution DNS locale | Entrer `intranet.iris.local` dans le navigateur (nécessite l'entrée hosts ou DNS actif). | Le VirtualHost intercepte la requête et affiche la page, aucune erreur 404. | ✅ Validé |
| **T6** | **Système**| Coexistence des services | Redémarrer complètement le serveur Linux (`sudo reboot`). | Au redémarrage, `kea-dhcp4-server` et `apache2` se lancent automatiquement sans conflit de ports. | ✅ Validé |

---

## 3. Synthèse de la Recette

Le serveur Debian remplit parfaitement ses rôles :
- La gestion manuelle des adresses IP est supprimée. L'utilisation de Kea DHCP offre une réponse extrêmement rapide et des fichiers de configuration bien plus lisibles pour l'équipe technique.
- Le serveur web Apache2 distribue le contenu statique sans aucune latence.

**Décision finale :** Le serveur d'infrastructure est déclaré opérationnel pour servir les postes du réseau de l'école.
