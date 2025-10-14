+++
date = '2025-10-05T10:57:30+01:00'
draft = true
title = "Keycloak : Part 2"
description = "Keycloak open-source Identity and Access Management (IAM) solution"
tags = ["Keycloak", "SOC Analyst", "IAM", ""]
slug = ""
categories = ["cybersecurity"]
series = []
+++

# Keycloak Workshop: Part 2 - Hands-On Setup & Core Concepts

Excellent! This is a great foundation for Part 2 of your Keycloak workshop. Let me help you expand this into a comprehensive lesson structure.

Major Topics
- What is Keycloak?
- How to install
- Keycloak realms, users, & groups
- Clients & Roles
- Protocol mappers & scopes
- Intro to IdPs and more ..

# Keycloak Workshop: Part 2 - Setup & Core Concepts

## Workshop Overview
In this session, we'll move from theory to practice by setting up Keycloak and exploring its fundamental components through hands-on exercises.

## Prerequisites
- Basic understanding of authentication concepts
- Java 11+ installed on your system
- Git installed
- A modern web browser

---

## Section 1: Keycloak Installation & Initial Setup

### Step 1: Download and Extract Keycloak
```bash
# Download Keycloak (if not already done)
wget https://github.com/keycloak/keycloak/releases/download/21.0.0/keycloak-21.0.0.zip

# Extract the archive
unzip keycloak-21.0.0.zip
cd keycloak-21.0.0/bin
```

### Step 2: Start Keycloak in Development Mode
```bash
# Start Keycloak in development mode (auto-configures for testing)
./kc.sh start-dev
```

**What happens in development mode:**
- Uses H2 embedded database
- Auto-creates admin user
- Enables development features
- Disables some security constraints for testing

### Step 3: Access Keycloak Admin Console
1. Open your browser and navigate to: `http://localhost:8080`
2. You should see the Keycloak welcome page
3. Click "Administration Console"

### Step 4: Create Admin Account
- Create your initial admin credentials:
  - Username: `admin` (or your choice)
  - Password: `admin123` (use a strong password in production!)
  - Confirm password

**Security Note:** In production, always use strong, unique passwords!

---

## Section 2: Understanding Keycloak Realms

### What is a Realm?
A **realm** in Keycloak is a security boundary that contains:
- Users
- Groups
- Clients
- Roles
- And other security configurations

Think of it as a **tenant** or **workspace** for your applications.

---

## Section 3: Two Setup Methods - Choose Your Path

### Option A: Import Pre-configured Realm (Recommended for Beginners)

```bash
# Clone the workshop repository
git clone https://github.com/andifalk/secure-oauth2-oidc-workshop.git oidc_workshop

# Import the realm configuration
# In Keycloak Admin Console:
# 1. Navigate to your realm
# 2. Go to Realm Settings → Import
# 3. Select file: oidc_workshop/setup/keycloak_realm_workshop.json
# 4. Choose "Import" strategy
```

**What this imports:**
- Pre-configured clients
- Sample users and roles
- Protocol mappers
- Ready-to-use configuration

### Option B: Manual Configuration (For Deep Learning)

We'll build everything from scratch to understand each component.

1. **Access Master Realm**: You're currently in the `master` realm (for managing Keycloak itself)
2. **Create New Realm**:
   - Click the current realm name (top-left dropdown)
   - Click "Create realm"
   - Name: `workshop-realm`
   - Click "Create"

3. **Switch to Workshop Realm**: Use the realm dropdown to switch to your new realm

### Realm Configuration Options
- **Tokens**: Token expiration settings
- **Login**: Authentication flows
- **Email**: SMTP configuration
- **Themes**: Customizing login pages
- **Security Defenses**: Brute-force protection

 



