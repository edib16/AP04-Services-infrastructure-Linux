# Dossier de Choix Technique - Services d'infrastructure Linux

> **Auteur :** Edib Saoud
> **Date :** 02/2025 - 03/2025
> **Version :** 1.0
> **Statut :** Validé

---

## 1. Contexte et Problématique

### Situation de l'existant
Le réseau pédagogique ne disposait pas de serveur DHCP centralisé. Lors du branchement de nouvelles machines, les étudiants devaient configurer les adresses IP manuellement, ce qui entraînait régulièrement des conflits d'adresses IP. De plus, aucune plateforme web locale ne permettait d'héberger la documentation de l'école.

### Besoin
Mettre en place un serveur capable d'attribuer automatiquement et dynamiquement les adresses IP aux clients (DHCP) et d'héberger une page HTML d'accueil (Intranet).

---

## 2. Choix d'Architecture

### 2.1 Système d'Exploitation
La distribution **Debian Linux** a été choisie pour sa stabilité, sa consommation mémoire extrêmement faible (idéale pour des services d'infrastructure continus) et son importante communauté.

### 2.2 Service DHCP : Le choix de Kea DHCP

| **Solution** | **Avantages** | **Inconvénients** | **Choix** |
|:---|:---|:---|:---:|
| **ISC DHCP Server** | Historique, très connu | En fin de vie (End of Life), syntaxe vieillissante | ❌ |
| **Kea DHCP** | Moderne, configuration en JSON, développé par l'ISC, performant | Syntaxe différente nécessitant une formation | ✅ |

**Décision :** Afin de garantir la pérennité (maintenabilité à long terme), **Kea DHCP** a été sélectionné pour remplacer l'ancien standard ISC. Sa configuration via un fichier JSON est plus lisible et standardisée.

### 2.3 Service Web
**Apache2** a été retenu pour sa fiabilité éprouvée sur le service de pages statiques (HTML/CSS) et sa facilité de mise en œuvre de `VirtualHosts` sans nécessiter de base de données complexe.

### 2.4 Plan d'Adressage IP

- **Serveur Linux (SRV-LINUX-01) :** `192.168.20.10 /24`
- **Passerelle (Gateway) :** `192.168.20.254`
- **Plage DHCP distribuée :** `192.168.20.100` à `192.168.20.200`
- **Nom de domaine Intranet :** `http://intranet.iris.local`

---

## 3. Analyse des Risques et Atténuation

| Risque Identifié | Impact | Probabilité | Mesure de prévention |
|:---|:---:|:---:|:---|
| **Conflit de service DHCP (Rogue DHCP)** | Élevé | Moyen | Isoler la maquette lors des tests. S'assurer qu'aucune box internet ne distribue du DHCP sur le même VLAN. |
| **Baux IP saturés (Épuisement des adresses)** | Moyen | Faible | La plage (100 adresses) est surdimensionnée par rapport aux besoins. Le temps de bail est défini à 12h. |
| **Erreur de syntaxe JSON (Kea)** | Élevé | Fort | Utilisation d'outils de validation de syntaxe JSON avant le redémarrage du service DHCP. |
| **Indisponibilité de l'Intranet** | Faible | Faible | Surveillance de l'état du service `apache2`. Redémarrage automatique en cas de crash. |
