+++ 
draft = false
date = 2026-01-09T11:03:32+01:00
title = " Mitre Att&ck"
description = "Mitre Adverserial Tactics, Techniques and Common Knowledge"
tags = ["Attack", "Att&ck", "Mitre", ""]
slug = ""
authors = "Seclice"
categories = ["",""]
series = []
+++

**MITRE ATT&CK®** est une base de connaissances et un modèle de référence mondialement reconnus en cybersécurité, décrivant les **tactiques, techniques et procédures (TTP)** utilisées par les adversaires (attaquants, groupes de pirates) lors de cyberattaques.

### **En résumé :**
C'est une **matrice** qui catégorise et organise les comportements malveillants observés dans la réalité. Elle permet de comprendre **"comment les attaquants pensent et agissent"**, étape par étape.

---

### **Les concepts clés de la matrice ATT&CK :**

1.  **Tactiques (Tactics) :** Le **"POURQUOI"**. Ce sont les objectifs à court terme de l'attaquant pendant une opération. C'est l'intention derrière une action.
    *   *Exemples :* Accès initial, Exécution, Persistance, Élévation de privilèges, Exfiltration de données.

2.  **Techniques (Techniques) :** Le **"COMMENT"**. Ce sont les moyens concrets utilisés pour atteindre un objectif tactique.
    *   *Exemple :* Pour la tactique "Accès initial", une technique est **"Phishing par email"**.

3.  **Sous-techniques (Sub-techniques) :** Un niveau de détail plus fin pour décrire une technique.
    *   *Exemple :* La technique "Phishing" a pour sous-technique **"Pièce jointe malveillante"**.

4.  **Procédures (Procedures) :** Le **"DÉTAIL SPÉCIFIQUE"**. Ce sont les implémentations réelles, les outils et les méthodes spécifiques utilisés par un groupe d'attaquants particulier.
    *   *Exemple :* Le groupe APT29 utilise un document Word spécifique avec une macro malveillante pour réaliser un phishing.

---

### **À quoi sert MITRE ATT&CK ?**

*   **Pour les équipes bleues (Défense) :**
    *   **Améliorer la détection :** Vérifier si vos systèmes de surveillance (SIEM, EDR) peuvent repérer les techniques répertoriées.
    *   **Évaluer et prioriser les risques :** Cartographier les techniques auxquelles votre organisation est vulnérable.
    *   **Structurer la chasse aux menaces (Threat Hunting) :** Rechercher activement des indicateurs de compromission basés sur des TTP connus.
    *   **Améliorer les tests de sécurité :** Les équipes rouges peuvent simuler des attaques réalistes en s'alignant sur la matrice.

*   **Pour la communication :**
    *   Un langage commun et standardisé entre les analystes SOC, les pentesteurs, les responsables de la sécurité (CISO) et la direction.

---

### **Structure visuelle : La Matrice**
La représentation la plus courante est un tableau où :
*   **Les colonnes** sont les **Tactiques** (les étapes d'une cyberattaque, de gauche à droite).
*   **Les lignes/cases** contiennent les **Techniques** et **Sous-techniques**.
*   Il existe des matrices différentes pour différents environnements : **Enterprise** (réseaux, cloud, endpoints), **Mobile**, et **ICS** (systèmes industriels).

**Exemple de parcours simplifié d'un attaquant dans la matrice :**
1.  **Accès initial** → Technique : *Phishing*
2.  **Exécution** → Technique : *Exécution via l'interpréteur de commandes*
3.  **Persistance** → Technique : *Création d'un compte local*
4.  **Exfiltration** → Technique : *Transfert sur un service cloud*

---

### **Où la trouver et comment l'utiliser ?**
*   **Site officiel :** `attack.mitre.org`
*   **Outils dérivés populaires :**
    *   **CALDERA** : Plateforme de simulation d'attaque automatique basée sur ATT&CK.
    *   **MITRE ATT&CK Navigator** : Un outil web pour visualiser et annoter vos propres couvertures de détection face à la matrice.
    *   **SOC-Matrix** (ou Atomic Red Team) : Fournit des tests simples ("atomics") pour simuler chaque technique.

**En bref :** MITRE ATT&CK est **l'encyclopédie du savoir-faire adverse**. Elle est devenue la **"langue franque"** de la cybersécurité opérationnelle, permettant aux défenseurs de se mettre à la place de l'attaquant pour mieux se protéger.