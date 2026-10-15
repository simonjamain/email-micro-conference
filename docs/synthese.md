# Synthèse à distribuer : l'email

## Glossaire

| Terme | Définition |
|-------|-----------|
| MUA | Client de messagerie (Outlook, Thunderbird, webmail) |
| MSA / MTA | Serveurs qui reçoivent, relaient et livrent les messages (SMTP) |
| MDA | Composant qui dépose le message dans la boîte |
| MX | Enregistrement DNS indiquant le serveur de réception d'un domaine |
| SMTP | Protocole de transport des messages entre serveurs |
| IMAP / POP | Protocoles de lecture de la boîte |
| Bounce | Message d'échec de remise (code 5xx) |
| SPF | Liste DNS des serveurs autorisés à envoyer pour un domaine |
| DKIM | Signature cryptographique du message, clé publique dans le DNS |
| DMARC | Politique (none/quarantine/reject) et rapports, basée sur l'alignement avec le `From` |
| ARC | Préserve les résultats d'authentification lors des transferts |
| BIMI | Affichage du logo de l'expéditeur si DMARC est strict |
| MTA-STS / TLS | Chiffrement du transport entre serveurs |
| PTR | DNS inverse d'une IP, attendu par les serveurs destinataires |
| Blocklist | Liste d'IP ou domaines jugés abusifs |
| Warm-up | Montée en charge progressive d'une nouvelle IP |

## Grille de diagnostic

1. Bounce (code d'erreur) ou silence ?
   - 4xx : temporaire, le serveur réessaie.
   - 5xx : définitif, lire le message d'erreur.
2. SPF, DKIM et DMARC passent-ils ? (`Authentication-Results` dans les en-têtes)
3. L'IP ou le domaine est-il sur une liste noire ?
4. Le contenu ou le volume est-il suspect ?
5. Un outil tiers envoie-t-il au nom de la collectivité sans être déclaré dans SPF/DKIM ?

**Outils** : MXToolbox, mail-tester, Google Postmaster Tools.

## Informations à fournir dans un ticket

- Date et heure d'envoi
- Expéditeur et destinataire
- Message d'erreur exact
- En-têtes complets du message (si reçu)
