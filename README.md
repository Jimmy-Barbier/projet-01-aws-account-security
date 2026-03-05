# 🔐 Sécurisation d'un compte AWS Free Tier

![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazonaws)
![IAM](https://img.shields.io/badge/IAM-Security-blue)
![Status](https://img.shields.io/badge/Status-Terminé-green)

## 📋 Objectif du projet

Mettre en place les bonnes pratiques de sécurité et de gouvernance dès la création d'un compte AWS, conformément aux recommandations du **AWS Well-Architected Framework**.

Ce projet démontre qu'une infrastructure cloud sécurisée commence **avant même le premier déploiement**. Il constitue le socle de tous les projets AWS qui suivront.

---

## 🧠 Compétences démontrées

- Sécurisation d'un compte root AWS
- Gestion des identités et accès (IAM)
- Principe du moindre privilège
- Gouvernance et contrôle des coûts
- Audit et traçabilité avec CloudTrail

---

## 🏗️ Architecture de sécurité mise en place
```
Compte AWS
│
├── Root (coffre-fort — usage exceptionnel uniquement)
│   └── MFA activé
│
├── IAM Admin (usage quotidien)
│   ├── MFA activé
│   ├── Politique AdministratorAccess
│   └── URL de connexion personnalisée
│
├── Billing
│   ├── Alerte budget à 5$
│   └── Cost Explorer activé
│
└── CloudTrail
    └── Logs stockés dans bucket S3 dédié
```

---

## 📌 Étapes de configuration

### Étape 1 — Création du compte AWS Free Tier

> **Pourquoi :** Le compte root est le compte le plus privilégié d'AWS. Il dispose d'un accès total et irréversible à toutes les ressources. Utiliser une adresse email dédiée permet d'isoler ce compte critique. La carte virtuelle avec plafond bas protège contre les erreurs de facturation accidentelles.

- Création du compte sur [aws.amazon.com](https://aws.amazon.com)
- Adresse email dédiée au compte root
- Carte virtuelle avec plafond bas (Revolut / Lydia)

![Dashboard AWS après création](assets/01-dashboard-aws.png)

---

### Étape 2 — Sécurisation du compte Root

> **Pourquoi :** Le compte root est la cible prioritaire des attaquants. Sans MFA, un simple vol de mot de passe suffit à compromettre l'intégralité du compte AWS. Le MFA ajoute une deuxième couche d'authentification qui rend l'accès non autorisé quasi impossible.

- Activation du MFA sur le compte root (Google Authenticator / Authy)
- Définition d'une politique de mot de passe fort

![MFA root activé](assets/02-mfa-root.png)
![Politique de mot de passe](assets/03-password-policy.png)

---

### Étape 3 — Création de l'utilisateur IAM Admin

> **Pourquoi :** C'est le principe du moindre privilège appliqué à la gestion du compte. Le root ne doit servir qu'en dernier recours. Pour toutes les opérations quotidiennes, on utilise un utilisateur IAM dédié dont les actions sont traçables et auditables, contrairement au root.

- Création de l'utilisateur IAM dans **IAM → Utilisateurs**
- Attachement de la politique **AdministratorAccess**
- Activation du MFA sur l'utilisateur IAM
- Création d'une URL de connexion personnalisée

![Utilisateur IAM créé](assets/04-iam-admin.png)
![MFA IAM activé](assets/05-mfa-iam.png)
![URL connexion personnalisée](assets/06-iam-url.png)

---

### Étape 4 — Déconnexion Root / Connexion IAM Admin

> **Pourquoi :** À partir de ce moment, le compte root est mis en "coffre-fort". Ne plus l'utiliser au quotidien réduit drastiquement la surface d'attaque. En cas de compromission de l'utilisateur IAM admin, le root reste intact et permet de reprendre le contrôle du compte.

- Déconnexion du compte root
- Reconnexion avec l'utilisateur IAM admin
- ⚠️ **Le compte root ne sera plus utilisé après cette étape**

![Connexion IAM admin réussie](assets/07-connexion-iam.png)

---

### Étape 5 — Alerte Budget AWS

> **Pourquoi :** AWS facture à l'usage. Une erreur de configuration, une instance oubliée ou une attaque de type cryptojacking peuvent générer des coûts importants en quelques heures. Une alerte à 80% du seuil défini permet d'agir avant d'atteindre la limite.

- Création d'un budget mensuel à **5$** dans **Billing → Budgets**
- Alerte email configurée à **80%** du budget

![Budget et alerte configurés](assets/08-budget-alerte.png)

---

### Étape 6 — Activation CloudTrail

> **Pourquoi :** CloudTrail enregistre toutes les actions effectuées sur le compte AWS — qui a fait quoi, quand, depuis quelle adresse IP. C'est l'équivalent d'un journal d'audit. En cas d'incident de sécurité, CloudTrail permet de retracer exactement ce qui s'est passé.

- Création d'un trail dans **CloudTrail**
- Journalisation activée sur **toutes les régions**
- Logs stockés dans un **bucket S3 dédié**

![CloudTrail configuré](assets/09-cloudtrail.png)
![Bucket S3 logs](assets/10-s3-logs.png)

---

### Étape 7 — Activation Cost Explorer

> **Pourquoi :** Cost Explorer offre une visualisation détaillée des coûts AWS par service, par région et par période. Couplé aux alertes budget, il permet de comprendre précisément l'origine des coûts et d'optimiser les dépenses. Indispensable pour tout ingénieur cloud.

- Activation dans **Billing → Cost Explorer**
- Vérification de la remontée des données

![Cost Explorer activé](assets/11-cost-explorer.png)

---

### Étape 8 — Vérification finale

> **Pourquoi :** AWS fournit dans le tableau de bord IAM une liste de recommandations de sécurité. Vérifier que toutes sont au vert confirme que la configuration est conforme aux bonnes pratiques AWS avant de commencer à déployer des ressources.

- Vérification dans **IAM → Tableau de bord**
- Toutes les recommandations de sécurité AWS au vert ✅

![IAM dashboard au vert](assets/12-iam-dashboard-vert.png)

---

## ✅ Résultat

| Élément | Statut |
|---|---|
| MFA Root | ✅ Activé |
| MFA IAM Admin | ✅ Activé |
| Utilisateur IAM Admin | ✅ Créé |
| Root mis en coffre-fort | ✅ |
| Alerte budget | ✅ Configurée |
| CloudTrail | ✅ Actif |
| Cost Explorer | ✅ Activé |
| Recommandations IAM | ✅ Au vert |

---

## 🔗 Projets suivants

- [Projet 02 — VPC Multi-AZ via console AWS](#) *(à venir)*
- [Projet 03 — VPC Multi-AZ en Terraform](#) *(à venir)*

---

## 👤 Auteur

**Jimmy Barbier**
Cloud Engineer AWS | Sécurité Cloud | Remote

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Jimmy_Barbier-blue?logo=linkedin)](https://www.linkedin.com/in/jimmy-barbier-89740539a/)
[![Portfolio](https://img.shields.io/badge/Portfolio-jimmy--barbier.github.io-informational)](https://jimmy-barbier.github.io/portfolio/)
