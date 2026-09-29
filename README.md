<!-- YoRHa archive -->
```
▸ YoRHa // ARCHIVE — FT_TRANSCENDANCE
```

A multiplayer Pong web platform with accounts, live chat, tournaments and an AI opponent, split across Django, a Channels/Daphne WebSocket layer and a dedicated Python game server.

![Django](https://img.shields.io/badge/Django-Channels-4e4b42?style=flat-square) ![Docker](https://img.shields.io/badge/Docker-Compose-dad4bb?style=flat-square)

| UNIT DATA | |
|---|---|
| Type | 42 Lausanne common-core project (final) · team (4) |
| Stack | Python · Django · Django REST framework · Channels / Daphne · Redis · PostgreSQL · Nginx · vanilla JS (canvas) · Docker Compose |
| Status | ■ COMPLETE |

## ▸ Overview
ft_transcendence is the last project of the 42 common core: a single-page web app built around real-time Pong.
This version uses seven containers. Django serves the pages and the REST API. Daphne and Django Channels (with a Redis channel layer) carry chat and tournament updates. A separate asyncio `websockets` server runs the games and starts one Python process per match. Nginx terminates TLS and routes each path prefix to the right service.

## ▸ Features
- **Accounts**: registration, login/logout, custom user model, profile picture upload, password change, password reset by email, optional email validation (the `MAIL` flag in `user/views.py`)
- **Social**: friend invites, friends list, blocking, user search, last-active status, public dashboards (`/dashboard/<username>/`), game history
- **Chat**: general chat and personal conversations over WebSocket (`ws/chat/`), with image attachments
- **Pong**: rendered on an HTML canvas. Game modes are local, online 2-player, 4-player (square field) and vs. AI. Ball size, paddle size, acceleration and points to win can be set per game, and each user can pick their own colours
- **AI opponent** (`GameServer/game/bottibotto.py`): predicts where the ball will hit, including wall bounces, and moves its paddle there
- **Tournaments**: live bracket updates pushed over `ws/tournament/<id>/`
- **Terminal client** (`cli/`): logs in or registers over HTTPS, creates or joins a game and plays Pong in the terminal
- **Infra**: Nginx with a self-signed certificate generated at build time. It sends `/` to Django, `/ws/` to Daphne and `/wsGame` to the game server. PostgreSQL has a healthcheck, and Adminer is included for browsing the database

## ▸ Usage
Create a `.env` file at the repository root. Docker Compose loads it into the Postgres, Django and Daphne containers:

```bash
POSTGRES_DB=transcendence
POSTGRES_USER=<user>
POSTGRES_PASSWORD=<password>
POSTGRES_HOST=postgresql
POSTGRES_PORT=5432
# only needed if MAIL = True in TranServer/user/views.py
MAIL_USER=<smtp user>
MAIL_PWD=<smtp password>
```

```bash
make          # docker pull postgres, compose down, then compose up --build
make stop     # docker compose down
```

Then open `https://localhost` and accept the self-signed certificate. Adminer is on port `8080`.

Optional helpers:

```bash
make install                 # local venv (pyvenv/) with the Python deps
./GenerateUser.sh alice bob  # creates test users (password = username) on https://127.0.0.1

cd cli && bash install.sh && bash run.sh   # terminal client
```

> `make fclean` / `make re` run `delete`, which removes **every** Docker container, image, volume and network on the machine, not only this project's.

## ▸ Structure
```
TranServer/     Django project — apps: user, chat, game, tournament (+ ASGI routing)
GameServer/     standalone asyncio WebSocket game server, game logic, AI bot
static/         front-end JS/CSS served by Nginx (pong.js, tournament.js, chat…)
cli/            terminal client (prompt_toolkit, blessed, websockets)
nginx/ Django/ Daphne/ pythongameserv/   Dockerfiles and init scripts
docker-compose.yml · Makefile
```

## ▸ Squad
The team had four members, named in the (now commented-out) CLI splash screen in `cli/main.py`: **Cecile**, **David**, **Alexandre** ([alde-oli](https://github.com/alde-oli)) and **Paul**.
This repository was uploaded from the team repo in a single commit, so it doesn't keep a per-person history.

## ▸ Notes
- The settings are school-project grade: the Django `SECRET_KEY` is hard-coded and `ALLOWED_HOSTS = ["*"]`. Don't deploy this as-is.

---
<sub>▸ Archived by UNIT ALDE-OLI · [profile](https://github.com/alde-oli)</sub>
