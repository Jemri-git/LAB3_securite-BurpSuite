# LAB3_securite-BurpSuite
# Rapport d'Audit : Interception de Trafic Mobile avec Burp Suite

##  A) Informations générales
- **Titre :** Interception et analyse de trafic mobile
- **Date :** 19 Avril 2026
- **Analyste :** IKHERAZEN Maryam
- **Périmètre :** Émulateur de labo, cible autorisée
- **Outils utilisés :** - Burp Suite Community/Pro
    - Root Certificate Manager
    - Proxy Android (10.0.2.2:8081)

---

##  B) Résumé exécutif
Cette analyse a permis de configurer un environnement d'interception fonctionnel pour auditer les communications d'une application mobile. 
L'interception a révélé que des **données d'authentification sont transmises de manière lisible** une fois le certificat SSL cassé (via l'installation d'un certificat CA).
Le niveau de risque est **Élevé** si un attaquant parvient à installer un certificat sur l'appareil ou si l'application ne vérifie pas l'identité du serveur (SSL Pinning).

**Actions prioritaires recommandées :**
1. Implémenter le **SSL Pinning** pour empêcher l'interception par des proxys tiers.
2. Utiliser des mécanismes de sécurité supplémentaires (Network Security Configuration).
3. Obfusquer les paramètres de requêtes sensibles.

---

##  C) Configuration du Laboratoire
### 1. Configuration du Proxy de Burp Suite
<img width="1458" height="382" alt="burp_settings" src="https://github.com/user-attachments/assets/d8fbd555-004f-4e76-ad59-9f20e7a0e9e3" />

### 2. Configuration du Proxy & Certificat
Le flux réseau a été redirigé vers l'hôte et le certificat CA de Burp Suite a été installé pour permettre l'inspection du trafic HTTPS.

<p align="center">
  <img src="https://github.com/user-attachments/assets/a0cde234-da74-420a-a568-45c8dca45ba5" width="300" alt="Proxy Config" />
  <img src="https://github.com/user-attachments/assets/97604db6-d093-4166-914e-5639441058ea" width="300" alt="Root Manager" />
  <img src="https://github.com/user-attachments/assets/fd0949c1-05e1-47d3-b3d5-410ff828e36d" width="300" alt="Cert Install" />
</p>

---

## 🔍 D) Constats détaillés

### Constat #1 : Interception réussie du trafic HTTPS
**Sévérité :** Élevée  
**Description :** Malgré l'utilisation du protocole HTTPS, l'application ne vérifie pas l'intégrité de la chaîne de certification (absence de Pinning), permettant à Burp de déchiffrer le trafic.  
**Localisation :** Flux réseau entre l'APK et le Backend.  
**Impact potentiel :** Un attaquant en position MitM (Man-in-the-Middle) peut lire et modifier les données échangées.  

![Interception du trafic](https://github.com/user-attachments/assets/8884c05a-eb20-4d6f-a0b0-52fd4fa48e87)

### Constat #2 : Transmission d'identifiants en clair
**Sévérité :** Critique  
**Description :** Les identifiants (Login/Password) sont transmis dans le corps de la requête sans couche de chiffrement applicative supplémentaire.  
**Localisation :** Requête POST vers le point d'accès `/login` (ou équivalent).  
**Impact potentiel :** Compromission totale du compte utilisateur en cas d'interception réussie.

![Login et mdp interceptés](https://github.com/user-attachments/assets/162b19ef-2f45-4332-93ff-788f5c01755e)

---

## 📦 E) Annexes & Nettoyage
- **Permissions :** `INTERNET`, `ACCESS_NETWORK_STATE`.
- **Nettoyage :** Suppression du certificat Burp de l'émulateur et réinitialisation des paramètres proxy.
