#php #tools 

`Composer` - это мощный инструмент, который значительно упрощает управление зависимостями в `PHP` проектах.
#### 🔹 Install

```bash
# for Ubuntu
sudo apt update
sudo apt install curl php-cli php-mbstring git unzip
cd ~
curl -sS https://getcomposer.org/installer -o composer-setup.php
на сайте  https://composer.github.io/pubkeys.html 
# копируем хеш
HASH=544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061 (вставляем скопированый хэш)
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
composer

# или
# Установка глобально (рекомендуется)  
curl -sS https://getcomposer.org/installer | php  
sudo mv composer.phar /usr/local/bin/composer

# Проверка установки  
composer --version  
  
# Самообновление Composer  
composer self-update
```

> "roave/security-advisories": "dev-master"  - проверяет старые(опасные) пакеты

---
#### 🔹 Основные команды Composer
##### 🔸 Управление зависимостями
```bash
# Установка зависимостей (создает composer.lock)
composer install

# Установка без dev зависимостей
composer install --no-dev

# Обновление зависимостей
composer update

# Обновление конкретного пакета
composer update vendor/package

# Обновление с игнорированием платформенных требований
composer update --ignore-platform-reqs

# Добавление новой зависимости
composer require vendor/package

# Добавление dev зависимости
composer require --dev phpunit/phpunit

# Добавление с указанием версии
composer require "monolog/monolog:^3.0"

# Удаление зависимости
composer remove vendor/package

# Поиск пакетов
composer search monolog

# Информация о пакете
composer show monolog/monolog

# Показать дерево зависимостей
composer show --tree

# Показать только установленные пакеты
composer show --installed
```

##### 🔸 Работа с автозагрузкой
```bash
# Генерация автозагрузчика
composer dump-autoload

# Оптимизированная генерация (для продакшена)
composer dump-autoload --optimize

# Авторитетный автозагрузчик (для продакшена)
composer dump-autoload --classmap-authoritative

# APCu оптимизация
composer dump-autoload --apcu
```

##### 🔸 Валидация и диагностика
```bash
# Валидация composer.json
composer validate

# Строгая валидация
composer validate --strict

# Диагностика проблем
composer diagnose

# Проверка зависимостей
composer check-platform-reqs

# Анализ зависимостей пакета
composer depends vendor/package

# Обратные зависимости
composer prohibits vendor/package

# Почему пакет не может быть установлен
composer why-not vendor/package:^2.0
```
---
#### 🔹 Версионирование и ограничения

##### 🔸 Семантическое версионирование
```json
{
    "require": {
        "vendor/package": "1.0.0",      // Точная версия
        "vendor/package": ">=1.0",      // Больше или равно
        "vendor/package": ">=1.0 <2.0", // Диапазон
        "vendor/package": ">=1.0 <1.1 || >=1.2", // Множественные диапазоны
        
        // Caret (совместимые изменения)
        "vendor/package": "^1.2.3",     // >=1.2.3 <2.0.0
        "vendor/package": "^0.3",       // >=0.3.0 <0.4.0
        
        // Tilde (минорные изменения)
        "vendor/package": "~1.2.3",     // >=1.2.3 <1.3.0
        "vendor/package": "~1.2",       // >=1.2.0 <2.0.0
        
        // Wildcard
        "vendor/package": "1.*",         // >=1.0.0 <2.0.0
        "vendor/package": "1.2.*",       // >=1.2.0 <1.3.0
        
        //Dev версии
        "vendor/package": "dev-master",  // Мастер ветка
        "vendor/package": "dev-develop", // Develop ветка
        
        // Алиасы
        "vendor/package": "dev-master as 1.0.x-dev"
    }
}
```
##### 🔸 Продвинутые техники версионирования
```json
{
    "require": {
        // Inline алиас для стабильности
        "vendor/package": "dev-feature-branch as 1.0.0",
        
        // Несколько источников
        "vendor/package": "^1.0 || ^2.0",
        
        // Исключение конкретных версий
        "vendor/package": "^1.0,!=1.0.5,!=1.0.6"
    },
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/user/fork",
            "options": {
                "branch-alias": {
                    "dev-master": "1.0.x-dev"
                }
            }
        }
    ]
}
```
---
#### 🔹 Автозагрузка
##### 🔸 Различные стратегии автозагрузки
```json
{
    "autoload": {
        // PSR-4 автозагрузка (рекомендуется)
        "psr-4": {
            "App\\": "src/",
            "MyProject\\Database\\": "database/",
            "MyProject\\Tests\\": "tests/"
        },
        
        // PSR-0 автозагрузка (устаревший)
        "psr-0": {
            "Monolog\\": "src/",
            "Vendor\\Namespace\\": "src/"
        },
        
        // Карта классов
        "classmap": [
            "database/migrations",
            "database/seeds",
            "legacy/"
        ],
        
        // Подключение файлов
        "files": [
            "src/helpers.php",
            "src/constants.php"
        ]
    },
    
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/",
            "Database\\Factories\\": "database/factories/"
        }
    }
}
```

##### 🔸 Оптимизация автозагрузки
```php
// src/helpers.php - Пример файла с helper функциями
<?php

if (!function_exists('app_path')) {
    function app_path(string $path = ''): string 
    {
        return __DIR__ . '/app' . ($path ? DIRECTORY_SEPARATOR . $path : '');
    }
}

if (!function_exists('config')) {
    function config(string $key = null, $default = null) 
    {
        static $config = null;
        
        if ($config === null) {
            $config = require __DIR__ . '/config/app.php';
        }
        
        if ($key === null) {
            return $config;
        }
        
        return $config[$key] ?? $default;
    }
}

// Автозагрузка классов через spl_autoload_register
spl_autoload_register(function ($class) {
    $prefix = 'MyApp\\';
    $baseDir = __DIR__ . '/src/';
    
    $len = strlen($prefix);
    if (strncmp($prefix, $class, $len) !== 0) {
        return;
    }
    
    $relativeClass = substr($class, $len);
    $file = $baseDir . str_replace('\\', '/', $relativeClass) . '.php';
    
    if (file_exists($file)) {
        require $file;
    }
});
```
---
#### 🔹 Скрипты и хуки
##### 🔸 Определение пользовательских скриптов
```json
{
    "scripts": {
        // Встроенные хуки Composer
        "pre-install-cmd": [
            "echo 'Начинается установка зависимостей'"
        ],
        "post-install-cmd": [
            "@php artisan optimize",
            "MyProject\\Installer::postInstall"
        ],
        "pre-update-cmd": [
            "php -r \"copy('.env', '.env.backup');\""
        ],
        "post-update-cmd": [
            "@composer dump-autoload",
            "@php artisan migrate --force"
        ],
        "post-autoload-dump": [
            "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
            "@php artisan package:discover --ansi"
        ],
        
        // Пользовательские скрипты
        "test": [
            "@php vendor/bin/phpunit"
        ],
        "test-coverage": [
            "@php vendor/bin/phpunit --coverage-html coverage"
        ],
        "quality": [
            "@test",
            "@cs-check",
            "@analyse"
        ],
        "cs-check": [
            "php-cs-fixer fix --dry-run --diff"
        ],
        "cs-fix": [
            "php-cs-fixer fix"
        ],
        "analyse": [
            "@php vendor/bin/phpstan analyse"
        ],
        "deploy": [
            "composer install --no-dev --optimize-autoloader",
            "@php artisan config:cache",
            "@php artisan route:cache",
            "@php artisan view:cache"
        ],
        "fresh-install": [
            "composer install",
            "@php artisan key:generate",
            "@php artisan migrate:fresh --seed"
        ],
        
        // Скрипты с параметрами
        "migrate": [
            "@php artisan migrate"
        ],
        "migrate-fresh": [
            "@php artisan migrate:fresh --seed"
        ]
    },
    
    "scripts-descriptions": {
        "test": "Запуск тестов",
        "quality": "Проверка качества кода",
        "deploy": "Подготовка к развертыванию",
        "fresh-install": "Полная переустановка проекта"
    }
}
```

##### 🔸 Создание класса для сложных скриптов
```php
// src/ComposerScripts.php
<?php

namespace MyProject;

use Composer\Script\Event;
use Composer\Installer\PackageEvent;

class ComposerScripts
{
    public static function postInstall(Event $event): void
    {
        $vendorDir = $event->getComposer()->getConfig()->get('vendor-dir');
        $io = $event->getIO();
        
        $io->write('🎉 Установка завершена!');
        
        // Создание необходимых директорий
        $directories = [
            'storage/logs',
            'storage/cache',
            'public/uploads',
        ];
        
        foreach ($directories as $dir) {
            if (!is_dir($dir)) {
                mkdir($dir, 0755, true);
                $io->write("✅ Создана директория: {$dir}");
            }
        }
        
        // Копирование конфигурационных файлов
        if (!file_exists('.env') && file_exists('.env.example')) {
            copy('.env.example', '.env');
            $io->write('✅ Скопирован файл .env');
        }
        
        // Установка прав доступа
        if (is_dir('storage')) {
            chmod('storage', 0775);
            $io->write('✅ Установлены права для storage');
        }
    }
    
    public static function preUpdate(Event $event): void
    {
        $io = $event->getIO();
        $io->write('🔄 Создание бэкапа перед обновлением...');
        
        // Создание бэкапа важных файлов
        $backupFiles = ['.env', 'config/app.php'];
        $backupDir = 'backup/' . date('Y-m-d-H-i-s');
        
        if (!is_dir($backupDir)) {
            mkdir($backupDir, 0755, true);
        }
        
        foreach ($backupFiles as $file) {
            if (file_exists($file)) {
                copy($file, $backupDir . '/' . basename($file));
                $io->write("💾 Сохранен бэкап: {$file}");
            }
        }
    }
    
    public static function postPackageInstall(PackageEvent $event): void
    {
        $installedPackage = $event->getOperation()->getPackage();
        $io = $event->getIO();
        
        $io->write("📦 Установлен пакет: {$installedPackage->getName()}");
        
        // Специфичные действия для определенных пакетов
        switch ($installedPackage->getName()) {
            case 'laravel/telescope':
                $io->write('🔭 Не забудьте выполнить: php artisan telescope:install');
                break;
                
            case 'barryvdh/laravel-debugbar':
                $io->write('🐛 Debug bar установлен. Настройте в .env: DEBUGBAR_ENABLED=true');
                break;
        }
    }
}
```
---
#### 🔹 Репозитории и источники пакетов
##### 🔸 Различные типы репозиториев
```json
{
    "repositories": [
        // VCS репозиторий (Git, SVN, Mercurial)
        {
            "type": "vcs",
            "url": "https://github.com/user/repository"
        },
        {
            "type": "vcs",
            "url": "git@github.com:user/private-repo.git"
        },
        
        // Локальный путь
        {
            "type": "path",
            "url": "./packages/my-package",
            "options": {
                "symlink": true
            }
        },
        
        // Частный Composer репозиторий
        {
            "type": "composer",
            "url": "https://repo.packagist.com/my-company/",
            "options": {
                "http": {
                    "header": [
                        "API-TOKEN: your-api-token"
                    ]
                }
            }
        },
        
        // Пакет из архива
        {
            "type": "package",
            "package": {
                "name": "custom/package",
                "version": "1.0.0",
                "dist": {
                    "url": "https://example.com/package.zip",
                    "type": "zip"
                },
                "source": {
                    "url": "https://github.com/user/repo.git",
                    "type": "git",
                    "reference": "master"
                },
                "autoload": {
                    "psr-4": {
                        "Custom\\Package\\": "src/"
                    }
                }
            }
        },
        
        // Артефакты (для legacy пакетов)
        {
            "type": "artifact",
            "url": "path/to/directory/with/zips/"
        }
    ]
}
```

##### 🔸 Конфигурация репозиториев
```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://private-packagist.com",
            "options": {
                "http": {
                    "timeout": 60,
                    "header": [
                        "Authorization: Bearer your-token"
                    ]
                },
                "ssl": {
                    "verify_peer": false
                }
            }
        }
    ],
    "config": {
        "github-oauth": {
            "github.com": "your-github-token"
        },
        "gitlab-oauth": {
            "gitlab.com": "your-gitlab-token"
        },
        "http-basic": {
            "private-repo.com": {
                "username": "user",
                "password": "pass"
            }
        }
    }
}
```
---
#### 🔹 Полезные команды и техники
##### 🔸 Продвинутые команды
```bash
# Создание проекта из шаблона
composer create-project laravel/laravel my-project
composer create-project --prefer-dist laravel/laravel blog

# Глобальные пакеты
composer global require laravel/installer
composer global require friendsofphp/php-cs-fixer
composer global show
composer global remove package/name

# Работа с версиями
composer outdated                    # Устаревшие пакеты
composer outdated --direct          # Только прямые зависимости
composer outdated --minor-only      # Только минорные обновления

# Анализ зависимостей
composer depends vendor/package      # Кто зависит от пакета
composer why vendor/package          # Алиас для depends
composer prohibits vendor/package    # Что блокирует пакет
composer why-not vendor/package      # Алиас для prohibits

# Сухой прогон операций
composer update --dry-run           # Показать что будет обновлено
composer require package/name --dry-run

# Работа с платформой
composer check-platform-reqs        # Проверка требований
composer update --ignore-platform-req=php    # Игнорировать PHP версию
composer update --ignore-platform-req=ext-gd # Игнорировать расширение

# Скорость и оптимизация
composer install --no-dev --optimize-autoloader --classmap-authoritative
composer install --apcu-autoloader  # Кеширование в APCu

# Безопасность
composer audit                       # Проверка безопасности
composer audit --format=json        # В JSON формате

# Информация и отладка
composer info                        # Список установленных пакетов
composer info vendor/package        # Детальная информация
composer info --tree                # Дерево зависимостей
composer info --self                # Информация о Composer
composer info --platform            # Платформенные пакеты

# Лицензии
composer licenses                    # Показать лицензии всех пакетов

# Конфигурация
composer config --list              # Все настройки
composer config repositories.foo vcs https://github.com/foo/bar
composer config extra.key value
composer config --unset repositories.foo

# Кеш
composer clear-cache                 # Очистить кеш
composer clearcache                  # Алиас
```
##### 🔸 Оптимизация для продакшена
```bash
# Полная оптимизация для продакшена
composer install \
    --no-dev \
    --optimize-autoloader \
    --classmap-authoritative \
    --apcu-autoloader \
    --no-scripts

# Создание архива проекта
composer archive --format=zip

# Проверка перед деплоем
composer validate --strict --no-check-all
composer audit
composer outdated --direct
```
---
#### 🔹 Хитрости и полезные техники

##### 🔸 Локальная разработка пакетов
```json
{
    "repositories": [
        {
            "type": "path",
            "url": "../my-package",
            "options": {
                "symlink": true
            }
        }
    ],
    "require": {
        "my/package": "@dev"
    }
}
```
##### 🔸 Форк пакета для исправлений
```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/my-username/forked-package"
        }
    ],
    "require": {
        "vendor/package": "dev-bugfix-branch"
    }
}
```
##### 🔸 Patch файлы для быстрых исправлений
```json
{
    "require": {
        "cweagans/composer-patches": "^1.7"
    },
    "extra": {
        "patches": {
            "vendor/package": {
                "Описание исправления": "patches/package-fix.patch"
            }
        }
    }
}
```
##### 🔸 Условная загрузка пакетов
```json
{
    "require": {
        "php": ">=8.1"
    },
    "require-dev": {
        "phpunit/phpunit": "^10.0",
        "mockery/mockery": "^1.5"
    },
    "suggest": {
        "ext-redis": "Для использования Redis кеша (установите: apt-get install php-redis)",
        "ext-imagick": "Для обработки изображений (установите: apt-get install php-imagick)"
    },
    "extra": {
        "laravel": {
            "dont-discover": [
                "barryvdh/laravel-debugbar"
            ]
        }
    }
}
```

##### 🔸 Многоэтапная установка
```json
{
    "scripts": {
        "build": [
            "@composer install --no-dev --optimize-autoloader",
            "@build-assets",
            "@optimize-config"
        ],
        "build-assets": [
            "npm ci",
            "npm run production"
        ],
        "optimize-config": [
            "@php artisan config:cache",
            "@php artisan route:cache",
            "@php artisan view:cache"
        ],
        "dev-setup": [
            "composer install",
            "cp .env.example .env",
            "@php artisan key:generate",
            "npm install",
            "npm run dev"
        ]
    }
}
```

##### 🔸 Профилирование Composer
```bash
# Профилирование установки
composer install --profile

# Подробный вывод для отладки
composer install -vvv

# Анализ времени выполнения
time composer install

# Использование альтернативного файла composer.json
composer install --working-dir=/path/to/project

# Установка с конкретного composer.json
composer install --file=composer.production.json
```
##### 🔸 Работа с приватными репозиториями
```bash
# Настройка авторизации для GitHub
composer config --global github-oauth.github.com YOUR_GITHUB_TOKEN

# Для GitLab
composer config --global gitlab-oauth.gitlab.com YOUR_GITLAB_TOKEN

# HTTP Basic авторизация
composer config --global http-basic.private-repo.com username password

# SSH ключи для Git
eval $(ssh-agent -s)
ssh-add ~/.ssh/id_rsa
```
##### 🔸 Composer.lock стратегии
```bash
# Обновление только hash файлов в lock
composer update --lock

# Обновление с сохранением точных версий
composer update --with-dependencies vendor/package

# Принудительное обновление конкретного пакета
composer require vendor/package:^2.0 --update-with-dependencies

# Проверка изменений в lock файле
composer install --dry-run
```

> ==Не игнорьте composer.lock== - https://medium.com/phpyh/dont-ignore-composer-lock-6f9fb3dc511a

---