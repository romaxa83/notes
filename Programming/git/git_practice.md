#git

#### 🔹 Основные техники работы с ветками
##### 🔸 Git Flow
```bash
# Создание новой функциональности
git checkout -b feature/user-authentication
git push -u origin feature/user-authentication

# Завершение работы над фичей
git checkout develop
git merge feature/user-authentication
git branch -d feature/user-authentication
```
##### 🔸 Feature Branch Workflow
```bash
# Создание ветки для задачи
git checkout -b fix/payment-validation

# Работа с коммитами
git add .
git commit -m "fix: улучшена валидация платежных данных"
git push origin fix/payment-validation
```
---
#### 🔹 Работа с коммитами
##### 🔸 Исправление последнего коммита
```bash
# Изменение сообщения коммита
git commit --amend -m "fix: исправлена валидация email адресов"

# Добавление файлов в последний коммит
git add forgotten-file.php
git commit --amend --no-edit
```

##### 🔸 Интерактивный rebase
```bash
# Объединение/редактирование последних 3 коммитов
git rebase -i HEAD~3

# В редакторе можно:
# pick - оставить коммит как есть
# squash - объединить с предыдущим
# edit - остановиться для редактирования
# drop - удалить коммит
```
---
#### 🔹 Решение конфликтов
##### 🔸 Типичный процесс разрешения конфликтов
```bash
# При слиянии возник конфликт
git merge feature/new-dashboard
# Auto-merging src/Dashboard.php
# CONFLICT (content): Merge conflict in src/Dashboard.php

# Открываем файл и видим:
```

```php
class Dashboard 
{
    public function getData() 
    {
<<<<<<< HEAD
        return $this->cache->get('dashboard_data');
=======
        return $this->database->fetchDashboardData();
>>>>>>> feature/new-dashboard
    }
}
```

```bash
# После ручного разрешения конфликта
git add src/Dashboard.php
git commit -m "merge: объединены изменения dashboard с кэшированием"
```
---
#### 🔹 Полезные команды для отладки
##### 🔸 Поиск проблемного коммита
```bash
# Git bisect для поиска багов
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
# Git автоматически переключается между коммитами
# После тестирования каждого:
git bisect good  # или git bisect bad
```

##### 🔸 Просмотр истории файла
```bash
# История изменений конкретного файла
git log --follow -p src/User.php

# Кто и когда изменял строки файла
git blame src/User.php

# Поиск коммитов по содержимому
git log -S "password_hash" --source --all
```
---
#### 🔹 Работа с удаленными репозиториями
##### 🔸 Синхронизация с upstream
```bash
# Добавление upstream репозитория
git remote add upstream https://github.com/original/repo.git

# Синхронизация с основным репозиторием
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

##### 🔸 Работа с форками
```bash
# Обновление форка
git remote add upstream https://github.com/laravel/laravel.git
git fetch upstream
git rebase upstream/main
git push origin main --force-with-lease
```
---
#### 🔹 Продвинутые техники
##### 🔸  Stash для временного сохранения
```bash
# Сохранение незавершенной работы
git stash push -m "работа над авторизацией"

# Просмотр сохраненных изменений
git stash list

# Восстановление изменений
git stash pop
# или применение без удаления из stash
git stash apply stash@{0}
```
##### 🔸 Cherry-pick для переноса коммитов
```bash
# Перенос конкретного коммита в текущую ветку
git cherry-pick abc123def

# Перенос диапазона коммитов
git cherry-pick feature-branch~3..feature-branch~1
```
##### 🔸 Работа с тегами
```bash
# Создание аннотированного тега
git tag -a v2.1.0 -m "Релиз версии 2.1.0 с новым API"

# Отправка тегов на сервер
git push origin v2.1.0
# или все теги
git push origin --tags
```
---
#### 🔹 Реальные кейсы из практики
##### 🔸  Горячие исправления (Hotfix)
```bash
# Срочное исправление на продакшене
git checkout main
git checkout -b hotfix/critical-security-fix
# Вносим изменения
git commit -m "security: исправлена уязвимость SQL инъекции"
git checkout main
git merge hotfix/critical-security-fix
git tag -a v2.0.1 -m "Критическое обновление безопасности"
git push origin main --tags
```
##### 🔸 Откат проблемного коммита
```bash
# Безопасный откат через revert
git revert abc123def
git push origin main

# Или откат нескольких коммитов
git revert --no-commit HEAD~3..HEAD
git commit -m "revert: откат проблемных изменений в платежной системе"
```
##### 🔸 Очистка истории перед merge
```bash
# Сжатие коммитов в feature ветке
git checkout feature/complex-feature
git rebase -i HEAD~5
# Объединяем мелкие коммиты в логические блоки
git push --force-with-lease origin feature/complex-feature
```
---
#### 🔹 Настройка для команды
##### 🔸  Полезные алиасы
```bash
# Добавление в ~/.gitconfig
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
```
##### 🔸 Hooks для автоматизации
```bash
# pre-commit hook для проверки кода
#!/bin/sh
# .git/hooks/pre-commit
npm run lint
php vendor/bin/phpunit --testsuite=unit
```
---

