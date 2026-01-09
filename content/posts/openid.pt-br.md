+++ 
draft = false
date = 2025-10-14T11:48:26+01:00
title = " OpenID Connect (OIDC) "
description = "OpenID Connect (OIDC) : La couche d'identité qui simplifie l'authentification moderne "
tags = ["OIDC", "IAM", "Authentication", ""]
slug = ""
authors = "Seclice"
categories = ["OIDC","IAM"]
series = []
+++


# **OpenID Connect (OIDC) : La couche d'identité qui simplifie l'authentification moderne**

## **Introduction**

Si OAuth 2.0 résout le problème de l'autorisation, **OpenID Connect (OIDC)** vient compléter le tableau en standardisant l'authentification. C'est le protocole qui permet véritablement de "se connecter avec Google, Facebook, Microsoft..." en toute sécurité. Dans cet article, nous explorerons comment OIDC transforme OAuth 2.0 en un système d'authentification fiable.

## **Qu'est-ce qu'OpenID Connect ?**

OpenID Connect est une **couche d'identité** simple construite au-dessus d'OAuth 2.0. Alors qu'OAuth 2.0 répond à la question "Cette application peut-elle accéder à mes données ?", OIDC répond à "**Qui est cet utilisateur ?**".

### **La relation OAuth 2.0 / OIDC**

- **OAuth 2.0** = Autorisation (accès aux ressources)
- **OIDC** = Authentication (vérification d'identité)
- **OIDC étend OAuth 2.0** en ajoutant un nouveau token : le **ID Token**

## **Le flux OpenID Connect en diagramme**

Voici comment OIDC enrichit le flux OAuth 2.0 standard :

{{<mermaid>}}
sequenceDiagram
    participant U as Utilisateur
    participant C as Client (RP)
    participant AS as Serveur OIDC (OP)
    participant RS as Serveur de Ressources

    title: Flux OpenID Connect - Authorization Code Flow

    Note over U,C: Étape 1 : Initiation avec scope openid
    U->>C: Clique sur "Se connecter"
    C->>U: Redirection vers OP

    Note over C,AS: Étape 2 : Demande d'autorisation OIDC
    C->>AS: GET /authorize
    Note right of C: scope=openid profile email<br>response_type=code<br>client_id=123, nonce=xyz

    Note over U,AS: Étape 3 : Authentification & Consentement
    AS->>U: Page de connexion
    U->>AS: Saisit credentials
    AS->>U: Page consentement OIDC
    U->>AS: Autorise

    Note over AS,C: Étape 4 : Retour avec code
    AS->>C: Redirection avec code
    Note left of AS: code=ABC123

    Note over C,AS: Étape 5 : Échange de tokens OIDC
    C->>AS: POST /token
    Note right of C: code, client_secret,<br>grant_type=authorization_code
    AS->>C: Response OIDC
    Note left of AS: id_token=JWT...<br>access_token=XYZ,<br>refresh_token=RTK

    Note over C,AS: Étape 6 : Récupération profil utilisateur
    C->>AS: GET /userinfo
    Note right of C: Authorization: Bearer XYZ
    AS->>C: UserInfo Response
    Note left of AS: {name, email, picture...}

    C->>U: Authentification réussie
    Note over C,U: Session créée avec identité utilisateur
{{</mermaid>}}

## **Les composants clés d'OIDC**

### **1. Le ID Token (JWT)**

Le **ID Token** est le cœur d'OIDC. C'est un JSON Web Token (JWT) contenant les claims d'identité de l'utilisateur :

```json
{
  "iss": "https://accounts.google.com",
  "sub": "1234567890",
  "aud": "client-123",
  "exp": 1712345678,
  "iat": 1712342078,
  "nonce": "xyz123",
  "name": "Jean Dupont",
  "email": "jean.dupont@email.com",
  "picture": "https://photo.jpg"
}

```

### **2. Les scopes standards**

OIDC introduit des scopes spécifiques :
- `openid` : Obligatoire pour utiliser OIDC
- `profile` : Accès au nom, prénom, photo
- `email` : Accès à l'adresse email
- `address` : Accès à l'adresse
- `phone` : Accès au numéro de téléphone

### **3. Les endpoints découverts**

OIDC utilise la découverte automatique via le endpoint `.well-known/openid-configuration` :

GET https://accounts.google.com/.well-known/openid-configuration


## **Les rôles dans OIDC**

- **End User** : L'utilisateur final
- **Relying Party (RP)** : L'application cliente (votre application)
- **OpenID Provider (OP)** : Le fournisseur d'identité (Google, Microsoft, etc.)

## **Pourquoi utiliser OIDC ?**

### **Avantages pour les développeurs**
- **Standardisation** : Protocole universel pour l'authentification
- **Simplicité** : Plus besoin de gérer les mots de passe
- **Sécurité** : Basé sur OAuth 2.0 avec tokens JWT
- **Interopérabilité** : Fonctionne avec tous les fournisseurs OIDC

### **Avantages pour les utilisateurs**
- **Expérience utilisateur** : Connexion en un clic
- **Sécurité** : Authentification forte chez le fournisseur
- **Contrôle** : Gestion centralisée des comptes
- **Confidentialité** : Partage minimal d'informations

## **Implémentation typique**

### **Côté client**
```javascript
// Configuration OIDC
const oidcConfig = {
  authority: 'https://accounts.google.com',
  client_id: 'votre-client-id',
  redirect_uri: 'https://yourapp.com/callback',
  response_type: 'code',
  scope: 'openid profile email'
};

// Redirection vers le fournisseur
function login() {
  window.location.href = `https://accounts.google.com/authorize?${new URLSearchParams(oidcConfig)}`;
}
```

### **Validation du ID Token**
```javascript
function validateIdToken(id_token) {
  // Vérifier la signature
  // Vérifier l'expiration
  // Vérifier l'audience
  // Vérifier le nonce
  const payload = JSON.parse(atob(id_token.split('.')[1]));
  return payload;
}
```

## **Bonnes pratiques de sécurité**

1. **Utiliser PKCE** pour les clients publics
2. **Valider scrupuleusement le ID Token**
3. **Utiliser des nonces** pour prévenir les attaques de replay
4. **Vérifier les audiences** (aud claim)
5. **Utiliser HTTPS** en production

## **Conclusion**

OpenID Connect représente l'évolution naturelle de l'authentification sur le web moderne. En combinant la puissance d'OAuth 2.0 avec une standardisation robuste de l'identité, OIDC offre :

- ✅ **Une expérience utilisateur seamless**
- ✅ **Une sécurité enterprise**
- ✅ **Une interopérabilité totale**
- ✅ **Une maintenance simplifiée**

Que vous construisiez une startup ou une enterprise application, OpenID Connect vous permet de déléguer la complexité de l'authentification pour vous concentrer sur la valeur métier de votre application.

**Avec OIDC, l'identité devient un service - fiable, sécurisé et universel.**