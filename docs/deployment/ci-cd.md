# CI/CD

::: details Что такое CI/CD?

CI/CD (Continuous Integration, Continuous Delivery — непрерывная интеграция и доставка) — это технология автоматизации билда, тестирования и развертывания разрабатываемого проекта.

Другими словами, процесс перехода лежащего в репозитории кода в рабочий продукт на облачном сервере, например.

Элементы CI/CD полезны не только девопс инженеру, но и простому фронтендеру, позволяя ему после git commit/push изменений кода получить рабочий сайт, например, на GitHub Pages без дополнительных действий.

Основные инструменты для CI/CD: GitHub Actions, GitLab CI/CD, Jenkins, Trevis

:::

::: details GitHub Actions - пример деплоя на GitHub Pages

`GitHub Actions` популярны и удобны, так что советуется их изучить для применения как в пет, так и в больших коммерческих проектах.

Ваш код должен быть на GitHub, естественно.

Добавьте в корень репозитория файл `.github/workflows/deploy.yaml`

В нем будут GitHub Actions инструкции.

Образец конфигурационного файла данного проекта (Vue-Faq)

```yaml
# .github/workflows/deploy.yaml

name: Build and Deploy
on:
  push:
    branches: [main]
permissions:
  contents: write
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v3

      - name: Install pnpm
        uses: pnpm/action-setup@v2
        id: pnpm-install
        with:
          version: 8.5.0
          run_install: false

      - name: Install dependencies
        run: pnpm install

      - run: pnpm build

      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          folder: docs/.vitepress/dist
          branch: gh-pages
```

После каждого пуша в репозиторий будет происходить сборка проекта и деплой новой версии на GitHub pages.

В данном случае используется три GitHub Actions: `actions/checkout@v3` для загрузка вашего кода из репозитория из ветки `main`, `pnpm/action-setup@v2` для установки `pnpm` менеджера, `JamesIves/github-pages-deploy-action@v4` для загрузки билда в `gh-pages` ветку вашего репозитория (она должна уже быть). Ваш GitHub проект должен быть настроен, чтобы GitHub Pages брали файлы из этой ветки (`Settings->Pages`).

:::

::: details GitHub Actions - пример деплоя на удаленный сервер по SSH

Более интересный случай полезной автоматизации.

У нас есть несколько окружений (`dev`, `staging`, `prod`) и деплоить в ручном режиме (используя `workflow_dispatch`) в нужное по выбору на удаленный сервер со своими параметрами.

Для каждого окружения у нас есть свой `.env` файл - `.env.dev`, `.env.staging` и `.env.prod` и каждому окружению соответствует своя директория на сервере.

Кроме того, там же на сервере есть директория `backup` с поддиректориями `dev`, `staging` и `prod`. При деплое мы будем бэкапить установленную версия в соответствующую директорию и хранить там 5 последних бэкапов для возможности отката при необходимости.

Кроме того, мы будем генерировать файл `build.json` с текущей датой и временем и возможно другой информацией. Он нужен как для информационной цели, так и позволяет решать проблему кэширования браузером файла `index.html` при апдейте сайта. Одно из решений - вебсайт при загрузке приложения скачиват этот файл и проверяет таймстемп в нем с тем, который он ранее сохранил в куках или localStorage. Если они не совпадают, то сайт форсирует перезагрузку `index.html`.

```yaml
# .github/workflows/deploy.yaml

name: Build and Deploy
on:
  push:
    branches: [main]
permissions:
  contents: write
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v3

      - name: Install pnpm
        uses: pnpm/action-setup@v2
        id: pnpm-install
        with:
          version: 8.5.0
          run_install: false

      - name: Install dependencies
        run: pnpm install

      - run: pnpm build

      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          folder: docs/.vitepress/dist
          branch: gh-pages
```

:::
