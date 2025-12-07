#git #devops #IaC

#### 🔹 CI/CD Pipeline Integration
##### 🔸 Автоматизация деплоя по тегам
```bash
# Создание релизного тега
git tag -a v1.2.3 -m "Production release 1.2.3"
git push origin v1.2.3

# В CI/CD pipeline (GitHub Actions пример)
```

```yml
name: Deploy to Production
on:
  push:
    tags:
      - 'v*'
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to production
        run: |
          echo "Deploying version ${{ github.ref_name }}"
          # Deployment scripts here
```

##### 🔸 Branching Strategy для DevOps
```bash
# GitFlow адаптированный для DevOps
git checkout -b release/1.2.0
# Стабилизация релиза
git checkout main
git merge release/1.2.0
git tag -a v1.2.0 -m "Release 1.2.0"
git checkout develop
git merge release/1.2.0
git branch -d release/1.2.0
```
---
#### 🔹 Infrastructure as Code (IaC)
##### 🔸 Версионирование инфраструктуры
```bash
# Структура репозитория инфраструктуры
infrastructure/
├── terraform/
│   ├── environments/
│   │   ├── production/
│   │   ├── staging/
│   │   └── development/
│   └── modules/
├── ansible/
│   ├── playbooks/
│   └── roles/
└── kubernetes/
    ├── manifests/
    └── helm-charts/
```

##### 🔸 Atomic deployments с Git
```bash
# Связывание деплоя с конкретным коммитом
git rev-parse HEAD > deployment-info.txt
echo "Deployed at: $(date)" >> deployment-info.txt
git add deployment-info.txt
git commit -m "deployment: production deploy $(git rev-parse --short HEAD)"
```
---
#### 🔹 Configuration Management
##### 🔸 GitOps подход
```bash
# Репозиторий конфигураций
configs/
├── applications/
│   ├── app-prod.yaml
│   ├── app-staging.yaml
│   └── app-dev.yaml
├── infrastructure/
│   └── cluster-config.yaml
└── policies/
    └── network-policies.yaml

# Автоматическая синхронизация
git checkout main
git pull origin main
# ArgoCD или Flux автоматически применяет изменения
```

##### 🔸 Environment-specific branches
```bash
# Отдельные ветки для окружений
git checkout -b config/production
git checkout -b config/staging
git checkout -b config/development

# Продвижение конфигурации между средами
git checkout config/staging
git merge config/development
git push origin config/staging
```
---
#### 🔹 Rollback и Recovery
##### 🔸 Быстрый откат деплоя
```bash
# Получение последнего рабочего тега
LAST_WORKING_TAG=$(git tag --sort=-version:refname | head -2 | tail -1)
echo "Rolling back to: $LAST_WORKING_TAG"

# Создание hotfix ветки для отката
git checkout $LAST_WORKING_TAG
git checkout -b hotfix/rollback-to-$LAST_WORKING_TAG
git push origin hotfix/rollback-to-$LAST_WORKING_TAG
```
##### 🔸 Blue-Green Deployment с Git
```bash
# Переключение между версиями
git checkout blue-environment
git merge main
git push origin blue-environment

# После успешного тестирования
git checkout green-environment
git merge blue-environment
git push origin green-environment
```
---
#### 🔹 Monitoring и Observability
##### 🔸 Связывание логов с коммитами
```bash
# Добавление метаданных в деплой
export GIT_COMMIT=$(git rev-parse HEAD)
export GIT_BRANCH=$(git rev-parse --abbrev-ref HEAD)
export BUILD_TIME=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

# В Dockerfile
LABEL git.commit=$GIT_COMMIT
LABEL git.branch=$GIT_BRANCH
LABEL build.time=$BUILD_TIME
```
##### 🔸 Automated changelog generation
```bash
# Генерация changelog между релизами
git log v1.1.0..v1.2.0 --pretty=format:"- %s (%an)" --no-merges > CHANGELOG.md

# Или с помощью conventional commits
git log --pretty=format:"%s" v1.1.0..HEAD | grep "^feat\|^fix\|^breaking" > release-notes.txt
```
---
#### 🔹 Security и Compliance
##### 🔸 Подписывание коммитов
```bash
# Настройка GPG подписи
git config --global user.signingkey YOUR_GPG_KEY_ID
git config --global commit.gpgsign true

# Подписанный коммит для критичных изменений
git commit -S -m "security: updated TLS certificates"
```
##### 🔸 Secret scanning prevention
```bash
# Pre-commit hook для проверки секретов
#!/bin/sh
# .git/hooks/pre-commit
if git diff --cached --name-only | xargs grep -l "password\|secret\|key" ; then
    echo "⚠️  Potential secrets detected!"
    echo "Please review your changes before committing."
    exit 1
fi
```
---
#### 🔹 Multi-Environment Management
##### 🔸 Feature flags через Git
```bash
# Структура feature flags
feature-flags/
├── production.json
├── staging.json
└── development.json

# Безопасное продвижение фич
git checkout main
git checkout -b feature-flag/new-payment-system
# Изменяем конфигурацию флагов
git commit -m "feat: enable new payment system for 10% users"
```
##### 🔸 Environment promotion pipeline
```bash
# Скрипт продвижения между окружениями
promote_environment() {
    local from_env=$1
    local to_env=$2
    
    git checkout $from_env
    git pull origin $from_env
    git checkout $to_env
    git merge $from_env --no-ff
    git push origin $to_env
    
    echo "Promoted from $from_env to $to_env"
}

# Использование
promote_environment "development" "staging"
promote_environment "staging" "production"
```
---
#### 🔹 Container и Microservices
##### 🔸 Multi-repo coordination
```bash
# Управление версиями микросервисов
services/
├── user-service/
├── payment-service/
├── notification-service/
└── docker-compose.yml

# Синхронизация версий
git submodule foreach git pull origin main
git add .
git commit -m "chore: update all microservices to latest"
```
##### 🔸 Container image versioning
```bash
# Автоматическое тегирование образов
GIT_TAG=$(git describe --tags --always)
docker build -t myapp:$GIT_TAG .
docker tag myapp:$GIT_TAG myapp:latest

# В CI/CD pipeline
echo "IMAGE_TAG=$GIT_TAG" >> $GITHUB_ENV
```
---
#### 🔹 Disaster Recovery
##### 🔸 Backup strategies с Git
```bash
# Создание backup репозитория
git clone --mirror https://github.com/company/critical-app.git backup-repo.git
cd backup-repo.git
git remote set-url origin https://backup-server/critical-app.git
git push origin --mirror
```
##### 🔸 Recovery procedures
```bash
# Процедура восстановления
restore_from_backup() {
    local backup_commit=$1
    git checkout $backup_commit
    git checkout -b emergency-restore-$(date +%Y%m%d-%H%M%S)
    git push origin emergency-restore-$(date +%Y%m%d-%H%M%S)
    echo "Emergency restore branch created"
}
```
---
#### 🔹 Automation Scripts
##### 🔸 DevOps helper scripts
```bash
#!/bin/bash
# deploy.sh - универсальный скрипт деплоя

ENVIRONMENT=${1:-staging}
VERSION=${2:-$(git describe --tags --always)}

echo "Deploying version $VERSION to $ENVIRONMENT"

# Проверка готовности
git fetch --tags
if ! git tag | grep -q "^$VERSION$"; then
    echo "Version $VERSION not found!"
    exit 1
fi

# Деплой
git checkout $VERSION
docker build -t myapp:$VERSION .
kubectl set image deployment/myapp myapp=myapp:$VERSION -n $ENVIRONMENT

echo "Deployment completed successfully"
```
---