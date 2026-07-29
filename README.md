# Backend PA GEN KANPE

API FastAPI autonome pour le MVP. Elle fournit l'authentification JWT, le contrôle d'accès par rôle, la file FIFO transactionnelle, les notifications internes, l'affichage public, les statistiques et un flux WebSocket.

## Prérequis

- Python 3.12+
- PostgreSQL 15+
- Docker

## Démarrage avec Docker

    docker build -t pagenkanpe-api .
    docker run --rm -p 8000:8000 \
      -e DATABASE_URL='postgresql+psycopg://USER:PASSWORD@HOST:5432/DATABASE' \
      -e JWT_SECRET='un-secret-aleatoire-d-au-moins-32-caracteres' \
      -e FRONTEND_ORIGINS='https://votre-frontend.example' \
      pagenkanpe-api

La base PostgreSQL doit être disponible via `DATABASE_URL`. Au démarrage, le conteneur applique les migrations puis démarre l'API. Aucun jeu de données de démonstration n'est créé.

L'API est disponible sur `http://localhost:8000`, Swagger sur `http://localhost:8000/docs` et la santé sur `http://localhost:8000/health`.

## Démarrage sans Docker

    python -m venv .venv
    source .venv/bin/activate
    pip install -e '.[dev]'
    cp .env.example .env
    alembic upgrade head
    uvicorn app.main:app --reload

## Tests

    pytest --cov=app
    ruff check app tests
    ruff format --check app tests

Par défaut, les tests utilisent SQLite pour rester rapides. Pour les tests d'intégration PostgreSQL :

    TEST_DATABASE_URL=postgresql+psycopg://postgres:postgres@localhost:5432/pagenkanpe_test pytest -m integration

## Variables importantes

| Variable | Description |
| --- | --- |
| `DATABASE_URL` | URL SQLAlchemy PostgreSQL |
| `JWT_SECRET` | Secret de signature, minimum 32 caractères |
| `FRONTEND_ORIGINS` | Origines CORS séparées par des virgules |
| `NO_SHOW_GRACE_SECONDS` | Délai avant marquage absent, 300 par défaut |
| `ACCESS_TOKEN_MINUTES` | Durée du JWT |

## Déploiement

En production, le conteneur applique uniquement les migrations avant de démarrer l'API. Il n'exécute aucun script de démonstration et ne crée donc aucun compte avec un mot de passe connu.

Le workflow CI valide Ruff, les tests sur PostgreSQL, les migrations et la couverture. Le workflow CD construit ensuite une image OCI et la publie dans GitHub Container Registry. Si le secret `STAGING_DEPLOY_WEBHOOK_URL` est défini, il déclenche aussi le déploiement de l'environnement de test.
