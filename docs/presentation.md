# L'email, de A à Z : comprendre pour mieux diagnostiquer

Micro-conférence (~20 min) — concept **1 phrase / 1 paragraphe / 1 page**.

**Fil rouge** : un mail d'un agent de la collectivité (`agent@ma-collectivite.fr`) vers un administré chez Gmail. On zoome sur ce même mail à chaque niveau.

| # | Section | Durée |
|---|---------|-------|
| 0 | Accroche et objectif | 1 min |
| 1 | Bref historique | 2 min |
| 2 | Niveau 1 : 1 phrase | 1 min |
| 3 | Niveau 2 : 1 paragraphe | 5 min |
| 4 | Niveau 3 : 1 page | 9 min |
| 5 | Diagnostiquer : grille pratique | 2 min |
| 6 | Conclusion et questions | 1 min |

---

## 0. Accroche et objectif (1 min)

- Situation vécue : « Mon mail n'est pas arrivé » / « Il est parti en spam ».
- Promesse : repartir avec un modèle mental pour comprendre les incidents.

## 1. Bref historique (2 min)

- **1971** : Ray Tomlinson envoie le premier email en réseau et choisit le `@`.
- **1982** : SMTP (RFC 821), conçu à une époque de confiance totale : aucun contrôle de l'expéditeur.
- **Années 90-2000** : explosion du spam.
- **Ensuite** : SPF, DKIM, DMARC, ajoutés après coup.
- **Message clé** : l'email est un protocole ancien auquel on a greffé de la sécurité.

## 2. Niveau 1 : « 1 phrase » (1 min)

> L'email est un système de courrier postal numérique où des serveurs se relaient pour acheminer un message jusqu'à la boîte du destinataire.

## 3. Niveau 2 : « 1 paragraphe » (5 min)

**Les acteurs**
- **MUA** (Mail User Agent) : Outlook, Thunderbird, webmail.
- **MSA/MTA** : serveurs d'envoi et de relais.
- **MDA / boîte** : stockage du message.
- **DNS** : enregistrements **MX** qui indiquent où livrer le courrier d'un domaine.

**Le trajet**
1. Envoi depuis le MUA vers le serveur de la collectivité.
2. Résolution MX du domaine du destinataire (`gmail.com`).
3. Relais SMTP vers le serveur destinataire.
4. Remise dans la boîte.
5. Lecture par le destinataire (IMAP/POP).

**Analogie postale** : l'enveloppe (SMTP) et la lettre (en-têtes et corps).

**Premières notions de problèmes** : file d'attente, rebond (bounce), délais.

## 4. Niveau 3 : « 1 page » (9 min)

### 4.1 Anatomie technique (2 min)

- Dialogue SMTP : `HELO` → `MAIL FROM` → `RCPT TO` → `DATA`.
- Enveloppe contre en-têtes : le `From` visible n'est pas le `MAIL FROM` (Return-Path).
- Lecture d'en-têtes : `Received` (de bas en haut), `Return-Path`, `Authentication-Results`.
- Codes de retour : **4xx** temporaire (le serveur réessaie), **5xx** définitif (rebond).

```
S: 220 mx.example.org ESMTP
C: HELO mail.ma-collectivite.fr
C: MAIL FROM:<agent@ma-collectivite.fr>
C: RCPT TO:<administre@gmail.com>
C: DATA
```

### 4.2 Authentification (3 min)

- **Problème** : usurpation d'identité (spoofing) et phishing, parce que SMTP ne vérifie rien.
- **SPF** : liste des serveurs autorisés à envoyer pour mon domaine (enregistrement DNS TXT). Il vérifie le `MAIL FROM`.
- **DKIM** : signature cryptographique du message ; la clé publique est dans le DNS. Elle prouve que le message n'a pas été modifié.
- **DMARC** : exige l'**alignement** avec le `From` visible et définit la politique (`none`, `quarantine`, `reject`) ; fournit des rapports.
- **Pièges courants**
  - SPF limité à **10 requêtes DNS**.
  - Transfert et listes de diffusion cassent SPF (et parfois DKIM).
  - Outils tiers (newsletter, GED, applications métier) qui envoient « au nom de » la collectivité.
- **Mention rapide** : ARC, BIMI, MTA-STS et TLS.

```
ma-collectivite.fr.  TXT  "v=spf1 include:_spf.exemple.net ip4:203.0.113.10 -all"
_dmarc.ma-collectivite.fr.  TXT  "v=DMARC1; p=none; rua=mailto:dmarc@ma-collectivite.fr"
```

### 4.3 Réputation et délivrabilité (3 min)

- **Réputation** de l'IP et du domaine : listes noires (blocklists), taux de plainte, spam traps.
- **Bonnes pratiques** : reverse DNS (PTR), warm-up, hygiène des listes, désabonnement facile.
- **Google et Yahoo (2024)** : exigences pour les gros volumes (SPF + DKIM + DMARC, désabonnement en un clic, taux de plainte bas).
- **Séparer les flux** (transactionnel, mailing, boîtes agents) pour protéger la réputation.
- **Filtrage côté destinataire** : antispam, scoring, quarantaine.

### 4.4 Contexte collectivité (1 min)

- Dépendance aux outils tiers.
- Enjeux de sécurité (recommandations ANSSI) et de RGPD.

## 5. Diagnostiquer : grille pratique (2 min)

1. **Bounce ou silence ?** Un bounce donne un code et un message ; le silence suggère un filtrage ou du spam.
2. **SPF, DKIM, DMARC OK ?** Voir `Authentication-Results`.
3. **IP ou domaine sur liste noire ?**
4. **Contenu suspect ?** Liens, pièces jointes, mailing massif.

**Outils** : MXToolbox, mail-tester, Google Postmaster Tools, lecture des en-têtes.

**Dans un ticket à la DSI** : heure d'envoi, expéditeur, destinataire, message d'erreur, en-têtes complets si possible.

**Démo (30 s)** : lecture d'en-têtes réels ou `dig TXT` / MXToolbox sur un domaine.

## 6. Conclusion et questions (1 min)

- **1 phrase** : l'email, c'est du courrier postal numérique relayé par des serveurs.
- **1 paragraphe** : un message passe par MUA, MTA, DNS (MX), puis la boîte.
- **1 page** : la confiance repose sur SPF/DKIM/DMARC et sur la réputation.
- **Plan d'action** : audit DMARC, inventaire des émetteurs tiers, support de référence.
- Questions.

---

## Conseils de mise en forme

- Un fil rouge visuel : le même mail à chaque niveau, avec un zoom progressif.
- Un schéma unique du trajet, enrichi à chaque couche.
- Une démo courte (30 s).
- Une page de synthèse à distribuer : [synthese.md](synthese.md).
- **Si le temps manque** : réduire le niveau 3 en gardant authentification et réputation.
