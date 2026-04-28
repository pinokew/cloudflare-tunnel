# CHANGELOG 2026 VOL 01

## [2026-04-28] — 

- **Context:** 
- **Change:** 
- **Verification:** 
- **Risks:** 
- **Rollback:** 

## [2026-04-28] — Ідемпотентне створення Swarm external network

- **Context:** GitHub Actions deploy падав на `docker stack deploy`, бо external network `proxy-net` не існувала як swarm-scoped network.
- **Change:** Додано `scripts/ensure-swarm-network.sh`, який читає `ORCHESTRATOR_ENV_FILE` без `source`, а за його відсутності визначає `dev/prod` через аргумент, `ENVIRONMENT_NAME` або `SERVER_ENV` і розшифровує `env.<env>.enc` через SOPS у `/dev/shm`. Скрипт ідемпотентно створює overlay attachable network і підключений як predeploy у `scripts/deploy-orchestrator-swarm.sh`. Production workflow також переведено з відсутнього `scripts/deploy-orchestrator.sh` на `scripts/deploy-orchestrator-swarm.sh`, щоб GitHub Actions не йшов у fallback.
- **Verification:** Пройдено `bash -n`, `shellcheck` і mock-перевірки сценаріїв створення, повторного запуску, SOPS-вибору `env.prod.enc`, некоректної наявної мережі та відсутнього середовища без реального впливу на production Docker.
- **Risks:** Якщо на хості вже існує мережа з такою назвою, але не `driver=overlay` або не `scope=swarm`, скрипт зупинить деплой з явною помилкою.
- **Rollback:** Видалити виклик `ensure_external_networks` із `scripts/deploy-orchestrator-swarm.sh` та прибрати `scripts/ensure-swarm-network.sh`.
