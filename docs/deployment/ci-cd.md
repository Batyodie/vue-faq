# CI/CD

::: details Что такое CI/CD?

CI/CD (Continuous Integration, Continuous Delivery — непрерывная интеграция и доставка) — это технология автоматизации билда, тестирования и развертывания разрабатываемого проекта.

Другими словами, процесс перехода лежащего в репозитории кода в рабочий продукт на облачном сервере, например.

Элементы CI/CD полезны не только девопс инженеру, но и простому фронтендеру, позволяя ему после git commit/push изменений кода получить рабочий сайт, например, на GitHub Pages без дополнительных действий.

Основные инструменты для CI/CD: GitHub Actions, GitLab CI/CD, Jenkins, Trevis

:::

::: details GitHub Actions

`GitHub Actions` популярны и удобны, так что советуется их изучить для применения как в пет, так и в больших коммерческих проектах.

Ваш код должен быть на GitHub, естественно.

Добавьте в корень репозитория файл `.github/workflows/deploy.yaml`

В нем будут GitHub Actions инструкции.

Образец конфигурационного файла данного проекта (Vue-Faq)

```yaml
name: Build and Deploy
on:
  push:
    branches: [main]
  workflow_dispatch:
    # branches: [ "main", "development" ]
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

      - run: pnpm docs:build

      - name: List website files
        run: ls docs/.vitepress/dist

      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          folder: docs/.vitepress/dist
          branch: gh-pages
```

После каждого пуша в репозиторий будет происходить сборка проекта и деплой новой версии на GitHub pages.

В общем случае возможна различная полезная автоматизация. Можно иметь несколько окружений (dev, staging, prod) и деплоить в нужное со своими параметрами. Можно бэкапить предыдущую версию сайта. Можно во время сборки билдить сайты с разными UI темами.

:::
