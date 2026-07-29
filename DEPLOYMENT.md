# Guide de déploiement

## Environnements

| Environnement | Déclencheur | Usage |
| --- | --- | --- |
| Local | Docker | Développement |
| Test | Pull request / branche `develop` | CI, migrations et tests |
| Production | Fusion dans `main` | Images versionnées dans GHCR |

## Backend

1. Créer une base PostgreSQL distincte par environnement.
2. Définir `DATABASE_URL`, un `JWT_SECRET` aléatoire d'au moins 32 caractères et les origines HTTPS.
3. Construire l'image depuis ce dossier puis fournir les variables à la plateforme ; aucune commande de démarrage surchargée n'est nécessaire.
4. L'image exécute les migrations puis Uvicorn ; elle ne crée aucun compte de démonstration.
5. Exposer `/health` au système de supervision.

## Frontend

1. Définir `BACKEND_URL` avec l'adresse interne ou privée de l'API.
2. Déployer l'image standalone Next.js derrière HTTPS.
3. Ne jamais exposer `JWT_SECRET` au frontend. Le JWT reçu de l'API reste dans un cookie HTTP-only.

## Secrets GitHub

- `STAGING_DEPLOY_WEBHOOK_URL` est facultatif. Lorsqu'il est présent, la CD appelle la plateforme d'hébergement après publication de l'image.
- `GITHUB_TOKEN` est fourni automatiquement pour publier dans GHCR.

## Vérifications après déploiement

1. `/health` répond 200.
2. Une inscription client aboutit au tableau client.
3. Un ticket numérique et un ticket physique apparaissent dans la même file.
4. Deux guichets ne peuvent pas appeler le même ticket.
5. L'écran public reflète un appel en moins de cinq secondes.
6. Les journaux ne contiennent ni mot de passe ni JWT.
