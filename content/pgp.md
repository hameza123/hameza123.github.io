---
title: "PGP Encryption"
date: 2023-08-28
layout: "pgp"
---

## My PGP Key

For secure communication, you can use my PGP public key to encrypt messages that only I can decrypt. This is particularly useful for sharing sensitive information.

### Download Public Key
[Download my public key](/pgp/public-key.asc) to encrypt messages or verify my digital signature.

### Key Fingerprint
Verify the authenticity of my public key using this fingerprint:
`E1E0 33C4 D53B D298 28CE  74FA BC18 568C B9E1 B49C`

## How to Use My PGP Key

### For Encryption
1. Import my public key into your PGP software: `gpg --import pgp-key.txt`
2. Compose your message in a text editor
3. Encrypt the message using my public key
4. Send the encrypted message via email

### For Verification
1. Import my public key as above
2. Verify the fingerprint matches the one shown on this page
3. Use `gpg --verify signature-file document-file` to verify signed documents

### Recommended Software
- **GPG Suite** (macOS): https://gpgtools.org/
- **Gpg4win** (Windows): https://www.gpg4win.org/
- **Kleopatra** (Linux): Typically included with distributions
- **Thunderbird** with Enigmail (Cross-platform email encryption)

## Why Use PGP Encryption?

PGP (Pretty Good Privacy) provides end-to-end encryption for your communications, ensuring that only the intended recipient can read your messages.

In the context of cybersecurity:
- Protects sensitive information from interception
- Verifies the identity of the sender (through digital signatures)
- Ensures message integrity (messages cannot be altered without detection)
- Essential for responsible disclosure of security vulnerabilities