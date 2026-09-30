# Coffre

Page statique qui **chiffre un secret avant de le transmettre**.

## Pourquoi

Un jeton collé dans une conversation est publié à plusieurs endroits : le
service de messagerie, le fournisseur du modèle, et l'historique de la session.
Impossible de reprendre ce qui a été exposé.

Cette page supprime le problème à la source : le secret est chiffré **sur
l'appareil**, avec une clé publique, *avant* de partir. Ce qui circule ensuite
est illisible pour tout le monde — l'hébergeur, le réseau, la messagerie.

Seule la **clé privée** correspondante permet de déchiffrer, et elle ne quitte
pas le serveur de l'assistant.

## Usage

1. Ouvrir la page.
2. Indiquer un nom (`github`, `vercel`, `discord`…).
3. Coller le secret dans le champ masqué.
4. Appuyer sur **Chiffrer**.
5. Transmettre le bloc obtenu, par n'importe quel canal.

## Ce que la page ne fait pas

- aucun script tiers, aucune police distante, aucune requête sortante ;
- aucun stockage : rien n'est conservé, ni sur l'appareil ni sur le serveur ;
- aucune donnée n'est envoyée nulle part — le chiffrement est local.

Vérifiable : la page tient en un seul fichier, sans dépendance.

## Crypto

RSA-OAEP 2048 bits, SHA-256, via `crypto.subtle` — l'API cryptographique native
du navigateur, sans bibliothèque. Le message est plafonné à **190 octets**, ce
qui couvre largement un jeton (40 octets pour GitHub, 60 pour Vercel).

## Clé publique

Elle est intégrée dans `index.html`, constante `CLE_PUBLIQUE`, au format SPKI
DER base64. Une clé publique ne permet que de **chiffrer** : la publier est sans
risque.

Pour changer de paire de clés, regénérer la paire côté serveur puis remplacer
cette constante :

```bash
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out cle-privee.pem
openssl pkey -in cle-privee.pem -pubout -outform DER | base64 -w0
```

## Licence

MIT — voir [`LICENSE`](LICENSE).
