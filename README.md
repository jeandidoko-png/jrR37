# AgencyFlow Backend — Guide de déploiement rapide

## Ce que ce backend fait

- **Proxy universel** : contourne les blocages CORS pour tous les appels API
- **OAuth 3 clics** : tes clients connectent Meta/Shopify/Google/TikTok en cliquant "Autoriser"
- **Auth JWT** : login sécurisé pour toi (admin) et tes clients
- **Token refresh automatique** : les tokens Meta et Google se renouvellent seuls
- **Données temps réel** : Shopify, Stripe, Meta, Google Ads, Klaviyo, TikTok

---

## Déploiement en 5 minutes

### 1. Forker sur GitHub
```
Bouton "Fork" en haut à droite de ce dépôt
```

### 2. Déployer sur Vercel
1. vercel.com → Add New Project
2. Importer ton fork GitHub
3. Framework Preset : **Other**
4. Deploy

### 3. Configurer les variables d'environnement
Dans Vercel → ton projet → Settings → Environment Variables :
- Copie chaque ligne de `.env.example`
- Remplace les valeurs par les tiennes

### 4. Générer le hash de ton mot de passe admin
Dans le terminal Vercel ou en local :
```bash
node -e "require('bcryptjs').hash('TON_MOT_DE_PASSE', 10).then(console.log)"
```
Colle le résultat dans `ADMIN_PASS_HASH`

### 5. Connecter AgencyFlow à ton backend
Dans `agencyflow-v14.html`, cherche :
```
https://api.allorigins.win/get?url=
```
Remplace par :
```
https://TON-PROJET.vercel.app/api/proxy?url=
```

---

## Endpoints disponibles

| Endpoint | Description |
|---|---|
| `POST /api/auth?action=login` | Login → retourne token JWT |
| `POST /api/auth?action=register` | Créer un compte client (admin requis) |
| `GET /api/auth?action=verify` | Vérifier token JWT |
| `GET /api/oauth?provider=meta&clientId=xxx` | Démarrer OAuth Meta |
| `GET /api/oauth?provider=shopify&clientId=xxx&shop=xxx` | Démarrer OAuth Shopify |
| `GET /api/oauth?provider=google&clientId=xxx` | Démarrer OAuth Google |
| `GET /api/oauth?provider=tiktok&clientId=xxx` | Démarrer OAuth TikTok |
| `GET /api/data?channel=shopify&clientId=xxx` | Données Shopify temps réel |
| `GET /api/data?channel=stripe&clientId=xxx` | Données Stripe temps réel |
| `GET /api/data?channel=meta&clientId=xxx` | Données Meta Ads temps réel |
| `GET /api/data?channel=google&clientId=xxx` | Données Google Ads temps réel |
| `GET /api/data?channel=tiktok&clientId=xxx` | Données TikTok temps réel |
| `GET /api/data?channel=klaviyo&clientId=xxx` | Données Klaviyo temps réel |
| `GET /api/proxy?url=xxx` | Proxy universel |

---

## Créer un compte client

Une fois déployé, depuis ton dashboard admin :
```bash
curl -X POST https://TON-PROJET.vercel.app/api/auth?action=register \
  -H "Authorization: Bearer TON_TOKEN_ADMIN" \
  -H "Content-Type: application/json" \
  -d '{"email":"client@email.fr","password":"motdepasse","name":"Nova Sport","role":"client","clientId":"nova"}'
```

Le client reçoit ses identifiants, se connecte sur `https://TON-PROJET.vercel.app/public/client.html`, et connecte ses canaux en 3 clics.

---

## Architecture

```
AgencyFlow (HTML local ou hébergé)
        ↕
  Vercel Backend
  ├── /api/auth     → JWT login/register
  ├── /api/oauth    → OAuth flows (Meta, Shopify, Google, TikTok)
  ├── /api/data     → Données temps réel (Shopify, Stripe, Meta...)
  └── /api/proxy    → Proxy universel
        ↕
  APIs (Meta, Google, Shopify, Stripe, Klaviyo, TikTok)
```
