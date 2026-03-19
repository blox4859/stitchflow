# codex-stitch-local

[![Node.js](https://img.shields.io/badge/node-%3E%3D22-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![npm](https://img.shields.io/badge/npm-supported-CB3837?logo=npm&logoColor=white)](https://www.npmjs.com/)
[![Skill](https://img.shields.io/badge/Codex-skill-1C6BFF)](./skills/stitch-design-local/SKILL.md)
[![License](https://img.shields.io/badge/license-not%20specified-lightgrey)](#license)

Portable local Stitch setup for Codex:

- a Codex skill in [`skills/stitch-design-local`](./skills/stitch-design-local)
- a local toolkit in [`stitch-starter`](./stitch-starter)
- a one-command installer in [`install.sh`](./install.sh)

Портативная локальная сборка Stitch для Codex:

- skill для Codex в [`skills/stitch-design-local`](./skills/stitch-design-local)
- локальный toolkit в [`stitch-starter`](./stitch-starter)
- установка одной командой через [`install.sh`](./install.sh)

## Table Of Contents

- [English](#english)
- [Русский](#русский)
- [Contributing](./CONTRIBUTING.md)

## English

### Overview

`codex-stitch-local` packages everything needed to run a local Stitch-based design workflow with Codex on any server without machine-specific paths.

It solves a practical problem: the original setup worked only on one machine because the skill referenced hardcoded home-directory paths. This repository makes the setup portable by:

- shipping the Codex skill and the runtime toolkit together
- installing into `${CODEX_HOME:-$HOME/.codex}`
- preserving local secrets during reinstall with `--force`
- keeping generated runs and secrets out of Git

### Who This Is For

Use this repository if you want to:

- install the `stitch-design-local` skill on another machine
- generate and iterate UI screens via the official `@google/stitch-sdk`
- keep HTML and screenshot artifacts locally
- make the setup reproducible for teammates or AI agents

### Repository Layout

```text
codex-stitch-local/
├── install.sh
├── skills/
│   └── stitch-design-local/
│       ├── SKILL.md
│       ├── agents/openai.yaml
│       ├── references/
│       └── workflows/
└── stitch-starter/
    ├── package.json
    ├── .env.example
    ├── scripts/
    └── README.md
```

### Requirements

- Linux or macOS shell environment
- Node.js `22+`
- npm
- Codex with skill loading enabled
- a valid `STITCH_API_KEY`

### Quick Start

Clone the repository and run the installer:

```bash
git clone https://github.com/yshishenya/codex-stitch-local.git
cd codex-stitch-local
bash install.sh
```

The installer will:

1. Copy the skill into `${CODEX_HOME:-$HOME/.codex}/skills/stitch-design-local`
2. Copy the toolkit into `${CODEX_HOME:-$HOME/.codex}/stitch-starter`
3. Create `.env` from `.env.example` if needed
4. Install Node dependencies
5. Run a smoke test with `npm run list` if `STITCH_API_KEY` is already configured

After installation:

1. Add `STITCH_API_KEY` to `${CODEX_HOME:-$HOME/.codex}/stitch-starter/.env` if it is empty
2. Restart Codex so the new skill is loaded
3. Use the skill or toolkit commands

### Installation Options

```bash
bash install.sh --force
bash install.sh --skip-npm
bash install.sh --skip-smoke
```

Option behavior:

- `--force`: overwrite an existing installation while preserving the current toolkit `.env`
- `--skip-npm`: copy files without installing dependencies
- `--skip-smoke`: skip `npm run list` even if an API key is present

### How To Use

#### In Codex

Ask Codex to use the skill explicitly:

```text
Use $stitch-design-local to generate a premium desktop dashboard for an internal analytics product.
```

Typical skill flow:

1. Codex rewrites the design brief into a stronger Stitch prompt
2. Codex runs the local toolkit from `${CODEX_HOME:-$HOME/.codex}/stitch-starter`
3. Stitch returns a project and screen
4. HTML and screenshot artifacts are saved under `runs/`

#### From The CLI

```bash
cd "${CODEX_HOME:-$HOME/.codex}/stitch-starter"
npm run list
npm run generate -- --prompt "A modern SaaS dashboard with sidebar and stat cards"
npm run edit -- --prompt "Make it more premium and add stronger typography"
npm run variants -- --prompt "Explore three different visual directions" --variant-count 3
```

#### Common Command Patterns

Generate a new screen:

```bash
npm run generate -- --prompt "B2B dashboard with sidebar, chart cards, and a strong hero header"
```

Generate inside an existing project:

```bash
npm run generate -- --project-id 123456789 --prompt "Pricing page with 3 plans"
```

Edit the latest screen:

```bash
npm run edit -- --prompt "Tighten spacing, improve typography, and make the visual hierarchy clearer"
```

Create variants:

```bash
npm run variants -- --prompt "Explore three different visual directions" --variant-count 3
```

### Output Files

Each run creates a folder under:

```text
${CODEX_HOME:-$HOME/.codex}/stitch-starter/runs/<timestamp>-<operation>-<slug>/
```

Typical artifacts:

- `result.json` or `variants.json`
- `screen.html`
- `screen.png`, `screen.jpeg`, or `screen.webp`
- `html-url.txt`
- `image-url.txt`

The most recent single-screen result is tracked in:

```text
${CODEX_HOME:-$HOME/.codex}/stitch-starter/runs/latest-screen.json
```

### Guidance For AI Agents

This repository is intended for both humans and agents. If you are using it from Codex or another agentic environment, follow these conventions:

- prefer the `stitch-design-local` skill over direct ad hoc CLI usage
- rewrite vague requests into a structured design prompt before calling Stitch
- default to `DESKTOP` unless the user clearly requests another device type
- prefer one screen plus three variants for first-pass exploration
- use `edit` instead of full regeneration when the structure is already close
- never print `STITCH_API_KEY` or `.env` contents
- always report where output files were saved

### Updating An Existing Installation

To reinstall the latest version from the repository:

```bash
git pull
bash install.sh --force
```

This will replace the installed skill and toolkit while preserving the existing toolkit `.env`.

### Troubleshooting

#### `STITCH_API_KEY is not set`

Add your key here:

```text
${CODEX_HOME:-$HOME/.codex}/stitch-starter/.env
```

Expected format:

```dotenv
STITCH_API_KEY=your_key_here
```

#### `Node.js 22+ is required`

Install Node.js 22 or newer, then rerun:

```bash
bash install.sh
```

#### Skill does not appear in Codex

Restart Codex after installation. Skills are loaded by Codex at startup.

#### Smoke test fails

Run manually:

```bash
cd "${CODEX_HOME:-$HOME/.codex}/stitch-starter"
npm run list
```

If that fails, verify:

- `.env` exists
- `STITCH_API_KEY` is valid
- network access is available

### Security Notes

- Do not commit `.env`
- Do not expose `STITCH_API_KEY` in logs, screenshots, or terminal recordings
- Generated `runs/` outputs may contain product ideas or internal UI concepts, so review before sharing

### Contributing

Contribution guidelines are available in [CONTRIBUTING.md](./CONTRIBUTING.md).

In short:

1. Fork the repository
2. Create a branch
3. Make focused changes
4. Test the installer and CLI
5. Open a pull request with a clear summary

### License

No license has been added yet. Until a license is published, treat reuse and redistribution as not automatically granted.

## Русский

### Обзор

`codex-stitch-local` собирает в одном репозитории всё, что нужно для локального workflow со Stitch и Codex на любом сервере без машинно-зависимых путей.

Он решает практическую проблему: исходная версия работала только на одной машине, потому что skill ссылался на захардкоженные пути в домашней директории. Этот репозиторий делает установку переносимой за счет того, что:

- поставляет skill и runtime-toolkit вместе
- ставит всё в `${CODEX_HOME:-$HOME/.codex}`
- сохраняет локальные секреты при переустановке через `--force`
- не тащит в Git секреты и сгенерированные артефакты

### Для Кого Это

Используйте этот репозиторий, если вам нужно:

- поставить `stitch-design-local` на другой сервер
- генерировать и редактировать UI-экраны через официальный `@google/stitch-sdk`
- хранить HTML и скриншоты локально
- сделать setup воспроизводимым для команды или ИИ-агентов

### Структура Репозитория

```text
codex-stitch-local/
├── install.sh
├── skills/
│   └── stitch-design-local/
│       ├── SKILL.md
│       ├── agents/openai.yaml
│       ├── references/
│       └── workflows/
└── stitch-starter/
    ├── package.json
    ├── .env.example
    ├── scripts/
    └── README.md
```

### Требования

- shell-окружение Linux или macOS
- Node.js `22+`
- npm
- Codex с поддержкой skills
- валидный `STITCH_API_KEY`

### Быстрый Старт

Склонируйте репозиторий и запустите установщик:

```bash
git clone https://github.com/yshishenya/codex-stitch-local.git
cd codex-stitch-local
bash install.sh
```

Установщик:

1. Скопирует skill в `${CODEX_HOME:-$HOME/.codex}/skills/stitch-design-local`
2. Скопирует toolkit в `${CODEX_HOME:-$HOME/.codex}/stitch-starter`
3. Создаст `.env` из `.env.example`, если нужно
4. Установит Node-зависимости
5. Запустит smoke test через `npm run list`, если `STITCH_API_KEY` уже настроен

После установки:

1. Добавьте `STITCH_API_KEY` в `${CODEX_HOME:-$HOME/.codex}/stitch-starter/.env`, если файл пустой
2. Перезапустите Codex, чтобы он подхватил новый skill
3. Используйте skill или CLI-команды toolkit-а

### Опции Установки

```bash
bash install.sh --force
bash install.sh --skip-npm
bash install.sh --skip-smoke
```

Что делают опции:

- `--force`: перезаписывает существующую установку, но сохраняет текущий `.env` toolkit-а
- `--skip-npm`: копирует файлы без установки зависимостей
- `--skip-smoke`: не запускает `npm run list`, даже если ключ уже есть

### Как Пользоваться

#### Внутри Codex

Можно явно попросить Codex использовать skill:

```text
Use $stitch-design-local to generate a premium desktop dashboard for an internal analytics product.
```

Обычный поток работы skill-а:

1. Codex усиливает и структурирует дизайн-бриф
2. Codex запускает локальный toolkit из `${CODEX_HOME:-$HOME/.codex}/stitch-starter`
3. Stitch возвращает проект и экран
4. HTML и скриншоты сохраняются в `runs/`

#### Через CLI

```bash
cd "${CODEX_HOME:-$HOME/.codex}/stitch-starter"
npm run list
npm run generate -- --prompt "A modern SaaS dashboard with sidebar and stat cards"
npm run edit -- --prompt "Make it more premium and add stronger typography"
npm run variants -- --prompt "Explore three different visual directions" --variant-count 3
```

#### Частые Сценарии Команд

Сгенерировать новый экран:

```bash
npm run generate -- --prompt "B2B dashboard with sidebar, chart cards, and a strong hero header"
```

Сгенерировать экран в существующем проекте:

```bash
npm run generate -- --project-id 123456789 --prompt "Pricing page with 3 plans"
```

Отредактировать последний экран:

```bash
npm run edit -- --prompt "Tighten spacing, improve typography, and make the visual hierarchy clearer"
```

Сделать варианты:

```bash
npm run variants -- --prompt "Explore three different visual directions" --variant-count 3
```

### Выходные Файлы

Каждый запуск создает папку:

```text
${CODEX_HOME:-$HOME/.codex}/stitch-starter/runs/<timestamp>-<operation>-<slug>/
```

Типичные артефакты:

- `result.json` или `variants.json`
- `screen.html`
- `screen.png`, `screen.jpeg` или `screen.webp`
- `html-url.txt`
- `image-url.txt`

Последний одиночный результат также пишется в:

```text
${CODEX_HOME:-$HOME/.codex}/stitch-starter/runs/latest-screen.json
```

### Рекомендации Для ИИ-Агентов

Репозиторий рассчитан и на людей, и на агентные среды. Если вы используете его из Codex или другого ИИ-агента, придерживайтесь этих правил:

- предпочитайте `stitch-design-local`, а не разовые ad hoc CLI-вызовы
- перед вызовом Stitch превращайте расплывчатый запрос в структурированный дизайн-промпт
- по умолчанию используйте `DESKTOP`, если пользователь явно не просил другое устройство
- для первого прохода лучше делать один экран и три варианта
- если структура уже близка к цели, используйте `edit`, а не полную регенерацию
- никогда не печатайте `STITCH_API_KEY` или содержимое `.env`
- всегда сообщайте, куда сохранены выходные файлы

### Обновление Уже Установленной Версии

Чтобы переустановить актуальную версию из репозитория:

```bash
git pull
bash install.sh --force
```

Это обновит skill и toolkit, сохранив текущий `.env`.

### Решение Проблем

#### `STITCH_API_KEY is not set`

Добавьте ключ сюда:

```text
${CODEX_HOME:-$HOME/.codex}/stitch-starter/.env
```

Ожидаемый формат:

```dotenv
STITCH_API_KEY=your_key_here
```

#### `Node.js 22+ is required`

Установите Node.js 22 или новее и повторите:

```bash
bash install.sh
```

#### Skill не появился в Codex

Перезапустите Codex после установки. Skills загружаются при старте.

#### Smoke test падает

Запустите вручную:

```bash
cd "${CODEX_HOME:-$HOME/.codex}/stitch-starter"
npm run list
```

Если команда падает, проверьте:

- что `.env` существует
- что `STITCH_API_KEY` валиден
- что есть доступ в сеть

### Безопасность

- не коммитьте `.env`
- не светите `STITCH_API_KEY` в логах, скриншотах и записях терминала
- папка `runs/` может содержать внутренние UI-концепты, поэтому проверяйте содержимое перед публикацией

### Контрибьютинг

Подробные правила описаны в [CONTRIBUTING.md](./CONTRIBUTING.md).

Коротко:

1. Сделайте fork
2. Создайте ветку
3. Вносите сфокусированные изменения
4. Проверьте installer и CLI
5. Откройте pull request с понятным описанием

### Лицензия

Лицензия пока не добавлена. До её публикации считайте, что автоматическое право на свободное переиспользование и распространение не предоставлено.
