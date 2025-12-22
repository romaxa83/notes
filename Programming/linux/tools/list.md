#linux #tools

#### 🔹 GUAKE (терминал)

```bash
sudo apt-get install guake -y
```

- info - https://losst.ru/emulyator-terminala-guake
---
#### 🔹 MC

```bash
sudo apt-get install mc -y
```

Файл настроек находиться `~/.config/mc/ini` (либо `config`).
- `skin` - устанавливает тему (`modarin256`)
- `menubar_visible=0` - убирает верхнее меню

==Установка в терминале 256-цветной дисплей==
- в терминал вставляем
	- `export TERM=xterm-256color`
	- в файл `~/.bashrc` (либо `~/.zshrc`) вставляем 
```bash
if [ -n "$DISPLAY" -a "$TERM" == "xterm" ]; then
    export TERM=xterm-256color
fi
```

-  source `~/.bashrc` (либо source `~/.shcrc`)
---
#### 🔹 SCREENFETCH

```bash
sudo apt-get install screenfetch -y
```
---
#### 🔹 PCManFM (файловый менеджер)

```bash
sudo apt-get install pcmanfm -y
```
---
#### 🔹 SHUTTER (скриншоты)

```bash
sudo apt-get install shutter -y

# OR

sudo add-apt-repository ppa:linuxuprising/shutter
sudo apt-get update
sudo apt install shutter
```

- редактирование скринов - http://ubuntuhandbook.org/index.php/2018/04/fix-edit-option-disabled-shutter-ubuntu-18-04/

==для редактирования==
```bash
wget https://launchpad.net/ubuntu/+archive/primary/+files/libgoocanvas-common_1.0.0-1_all.deb
wget https://launchpad.net/ubuntu/+archive/primary/+files/libgoocanvas3_1.0.0-1_amd64.deb
wget https://launchpad.net/ubuntu/+archive/primary/+files/libgoo-canvas-perl_0.06-2ubuntu3_amd64.deb
sudo dpkg -i libgoocanvas-common_1.0.0-1_all.deb
sudo dpkg -i libgoocanvas3_1.0.0-1_amd64.deb
sudo dpkg -i libgoo-canvas-perl_0.06-2ubuntu3_amd64.deb
sudo apt -f install
```
---
#### 🔹 FLAMESHOT (скриншот)

```bash
sudo add-apt-repository ppa:atareao/flameshot
sudo apt update
sudo apt install flameshot
```

Установка на `PrtSc`. Откройте настройки системы и перейдите в настройки клавиатуры. 
Здесь перечислены все сочетания клавиш. Пролистайте вниз и нажмите кнопку со значком `+`. В открывшемся окне вам надо будет ввести имя нового сочетания клавиш, а также команду запуска `Flameshot`
- /usr/bin/flameshot gui
отменяем `PrtS`c и добавляем для `flameshot`

- https://github.com/lupoDharkael/flameshot
-----------------------------------------------------------
#### 🔹DROPBOX

```bash
sudo apt-get update
sudo apt-get -y install nautilus-dropbox
```
-----------------------------------------------------------
#### 🔹TELEGRAM

```bash
sudo add-apt-repository ppa:atareao/telegram
sudo apt update
sudo apt install telegram
```
---
#### 🔹VLC (видео)

```bash
sudo apt install snapd
sudo snap install vlc
```
------------------------------------------------------------
#### 🔹 Diodon (увеличивает буфер обмена)

```bash
sudo apt-add-repository ppa:diodon-team/stable
sudo apt-get update
sudo apt-get install diodon
```
------------------------------------------------------------
#### 🔹 Htop

```bash
sudo apt install htop
```
  
- `t` - команды ввиде дерева
- `\ nameProccess` - фильтр по процессу
- `\ esc`  -  сброс фильтра
- `u` - выбираем пользователя
- убить процесс - выбираем процес , жмем K и 9
------------------------------------------------------------
#### 🔹 FIM
> просмотр изображения в консоли
 
 - https://andreyex.ru/operacionnaya-sistema-linux/kak-otobrazhat-izobrazheniya-v-terminale-v-linux/

```bash
sudo apt-get install fim

# использовать
fim -a dog.jpg
```
------------------------------------------------------------
#### 🔹 Утилита для сжатия jpeg картинок (jpegoptim)

- https://losst.ru/szhatie-png-v-linux

```bash
sudo apt-get install jpegoptim

# сжимаем картинку
jpegoptim file.jpeg --strp-all --dest=./min --max=80
# max  - указываем качество от основной картинки (%)
# dest  - куда поместить картинку

# выбираем все файла (папка /var/www/) и сжимаем все
find /var/www/ -name *.jpg -exec jpegoptim --strip-all '{}' \;
```
---
#### 🔹 Утилита для чтение электроных книг

- https://github.com/babluboy/bookworm
---
#### 🔹 УТИЛИТЫ

```bash
sudo apt install curl
sudo apt install make
sudo apt install unrar (утилита для распаковки rar-архивов)
sudo apt install gnome-tweaks (расширеные настройки гном окружения)
```
---