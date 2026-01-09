+++ 
draft = false
date = 2025-10-14T09:56:39+01:00
title = " Le protocole OAuth 2.0 "
description = "un article complet sur OAuth 2.0, conçu pour être compréhensible par les débutants et ceux qui ont besoin d'un rappel. "
tags = ["OAuth2", "IAM", "Authentication", ""]
slug = ""
authors = "Seclice"
categories = ["OAuth2","IAM"]
series = []
+++


 
### **OAuth 2.0 : Le protocole qui vous permet de vous connecter sans mot de passe**

Si vous avez déjà utilisé votre compte Google pour vous connecter à un service comme Spotify ou votre profil Facebook pour vous identifier sur un site de shopping, vous avez expérimenté OAuth 2.0 sans le savoir. C'est le protocole silencieux qui permet un accès sécurisé et sans mot de passe sur le web moderne et les applications mobiles.

Mais qu'est-ce que c'est exactement, et comment ça fonctionne ? Décomposons-le ensemble.

#### **Qu'est-ce qu'OAuth 2.0 ? (Et ce qu'il n'est PAS)**

Fondamentalement, **OAuth 2.0 est un cadre d'autorisation**. Il permet à une application tierce d'accéder aux ressources d'un utilisateur sur un autre service, **sans que l'utilisateur ait à partager son mot de passe.**

Cette distinction est cruciale :
*   **L'Authentification (AuthN)** consiste à prouver **"qui vous êtes"**. (Ex: se connecter avec un nom d'utilisateur et un mot de passe).
*   **L'Autorisation (AuthZ)** consiste à accorder la permission **"ce que vous pouvez faire"**. (Ex: autoriser un service de tirage photo à accéder à vos Google Photos).

OAuth 2.0 concerne principalement **l'autorisation**. Ce n'est pas un protocole d'authentification, bien qu'il soit souvent utilisé comme une brique de base pour des systèmes d'authentification comme **OpenID Connect (OIDC)**.

**Pourquoi est-ce si important ?**
1.  **Sécurité :** Vous n'avez pas à faire confiance à l'application tierce avec votre mot de passe sensible.
2.  **Contrôle de l'utilisateur :** Vous pouvez voir exactement quelles permissions (ou "portées") une application demande (ex: "Lire vos emails", "Accéder à vos contacts") et accorder ou révoquer cet accès à tout moment depuis le service principal (comme votre tableau de bord Google ou Facebook).
3.  **Commodité :** Pas besoin de créer et de mémoriser un nouveau mot de passe pour chaque service.

#### **Les acteurs clés : Les rôles dans OAuth 2.0**

Pour comprendre le processus, vous devez connaître les quatre rôles principaux :

1.  **Propriétaire de la Ressource :** C'est **vous**, l'utilisateur. Vous possédez les données et avez le pouvoir d'accorder l'accès.
2.  **Client :** L'**application tierce** qui veut accéder à vos données (ex: "Spotify.com").
3.  **Serveur de Ressources :** L'**API** qui détient les données de l'utilisateur (ex: les serveurs de Google qui stockent vos photos).
4.  **Serveur d'Autorisation :** Le serveur qui authentifie l'utilisateur et délivre les **jetons d'accès** après avoir obtenu son consentement. (Dans la pratique, c'est souvent le même que le Serveur de Ressources, comme c'est le cas pour Google).

#### **L'Analogie Classique : La Carte de Chambre d'Hôtel**

Imaginez que vous séjournez dans un hôtel (le **Client**).
1.  Vous (le **Propriétaire de la Ressource**) allez à la réception.
2.  Le réceptionniste (le **Serveur d'Autorisation**) vérifie votre identité avec votre passeport (votre mot de passe).
3.  On vous remet alors une carte magnétique (le **Jeton d'Accès**).
4.  Cette carte vous donne accès à votre chambre spécifique (vos **Ressources**) pour une durée limitée. Vous ne pouvez pas accéder au trousseau de clés principal ou au salon du personnel—seulement à ce pour quoi vous êtes autorisé.

L'hôtel ne vous donne pas la clé maîtresse (votre mot de passe) ; il vous donne un jeton temporaire aux permissions limitées.

#### **Le Flux OAuth 2.0 en 6 Étapes**

Prenons l'exemple de "ImprimeMesPhotos.com" qui veut accéder à vos images sur "Google Photos".

1.  **L'utilisateur Initie la Connexion :** Vous cliquez sur "Se connecter avec Google" sur ImprimeMesPhotos.com.
2.  **Demande d'Autorisation :** L'application ImprimeMesPhotos redirige votre navigateur vers le Serveur d'Autorisation de Google. Elle envoie son identifiant et les permissions dont elle a besoin (ex: `read:photos`).
3.  **Authentification et Consentement de l'Utilisateur :** Google vous invite à vous connecter (si vous ne l'êtes pas déjà). Il vous montre ensuite un écran disant : "ImprimeMesPhotos veut accéder à vos photos. Autoriser/Refuser ?"
4.  **Octroi d'Autorisation :** Si vous cliquez sur "Autoriser", Google vous redirige vers ImprimeMesPhotos.com avec un code spécial à usage unique appelé **Code d'Autorisation**.
5.  **Échange de Jetons :** ImprimeMesPhotos.com (depuis son serveur sécurisé) prend ce code, ainsi que sa propre clé secrète, et le renvoie au Serveur d'Autorisation de Google. En retour, Google renvoie un **Jeton d'Accès**.
6.  **Accès à la Ressource :** Désormais, chaque fois que ImprimeMesPhotos.com a besoin de récupérer vos photos, il envoie ce Jeton d'Accès avec sa requête à l'API Google Photos (le **Serveur de Ressources**). L'API vérifie le jeton et, s'il est valide, renvoie les photos demandées.


{{<mermaid>}}

 
sequenceDiagram
    participant U as Utilisateur
    participant C as Client
    participant AS as Serveur d'Autorisation
    participant RS as Serveur de Ressources

    title: Flux OAuth 2.0 - Authorization Code

    Note over U,C: Étape 1 : Initiation
    U->>C: Clique sur "Se connecter avec X"

    Note over C,AS: Étape 2 : Demande d'autorisation
    C->>AS: Redirection vers /authorize
    Note right of C: client_id, redirect_uri,<br>scope, response_type=code

    Note over U,AS: Étape 3 : Authentification & Consentement
    AS->>U: Page de connexion
    U->>AS: Saisit ses identifiants
    AS->>U: Page de consentement
    U->>AS: Clique sur "Autoriser"

    Note over AS,C: Étape 4 : Octroi d'autorisation
    AS->>C: Redirection avec code
    Note left of AS: code=A1B2C3...

    Note over C,AS: Étape 5 : Échange de jetons
    C->>AS: POST /token
    Note right of C: code, client_id,<br>client_secret, redirect_uri
    AS->>C: Réponse avec tokens
    Note left of AS: access_token=XYZ789...<br>token_type=Bearer, expires_in=3600

    Note over C,RS: Étape 6 : Accès aux ressources
    C->>RS: GET /api/resources
    Note right of C: Authorization: Bearer XYZ789...
    RS->>C: 200 OK + données
    
    C->>U: Connexion réussie
 
    
{{</mermaid>}}


#### **Les Types d'Octroi Courants (Flux)**

OAuth 2.0 a différents "flux" pour différents types d'applications :
*   **Flux "Authorization Code" (Code d'Autorisation) :** Le flux le plus sécurisé et le plus courant, utilisé par les applications web qui ont un composant côté serveur. C'est le flux décrit ci-dessus.
*   **Flux "Authorization Code" avec PKCE (prononcé "pixie") :** Une version améliorée du flux par code pour les applications mobiles et les applications à page unique (SPA) qui ne peuvent pas stocker de secret client de manière sécurisée.
*   **Flux "Client Credentials" (Identifiants Client) :** Utilisé pour l'authentification de machine à machine (M2M) où aucun utilisateur n'est présent. Par exemple, un service backend accédant à une autre API.
*   **Flux "Implicit" (Implicite - Déprécié) :** Utilisé auparavant pour les applications fonctionnant uniquement dans le navigateur, mais maintenant considéré comme non sécurisé et remplacé par le flux PKCE.

#### **Conclusion : Le Gardien Invisible**

OAuth 2.0 est devenu la pierre angulaire de l'identité numérique moderne et de la sécurité des API. En séparant les rôles de l'authentification et de l'autorisation, il crée une expérience plus sécurisée, centrée sur l'utilisateur et transparente à travers Internet. Il vous donne, en tant qu'utilisateur, le pouvoir de connecter votre monde numérique sans compromettre vos identifiants les plus sensibles.

La prochaine fois que vous cliquerez sur "Se connecter avec X", vous saurez qu'une poignée de main sophistiquée et sécurisée a lieu en arrière-plan, tout cela grâce à OAuth 2.0.