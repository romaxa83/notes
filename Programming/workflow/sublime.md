#tools

### 🔹 Install

```bash
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
sudo apt-add-repository "deb https://download.sublimetext.com/ apt/stable/"
sudo apt install sublime-text
```

- https://linuxconfig.org/how-to-install-sublime-text-on-ubuntu-18-04-bionic-beaver-linux
-----------------------------------------------------------------------
### 🔹 PLUGINS

- Side Bar Enhancements - плагин добавляет больше функционала в sidebar
- fileicons - плагин для иконок
- sublimeREPL - позволяет запускать интерпретатор для python (и для других языков)
`tools -> sublimeREPL -> python` (там будет `python2`), чтоб установить третью версию
`preferences -> Browse packages открываем файл SublimeRepl -> config -> python -> Main.sublime-menu` в секции `args {"cmd": ["python", ....]}` - это вызов питона, подставляем ту команду которая в системе используеться для вызова `python3`

-----------------------------------------------------------------------
### 🔹 Shortcuts

- `ctrl + `\` - открыть консоль
- `ctrl + b` - запустить скрипт
- `ctrl + shift + p` - открывается окно для установки новых пакетов
---
### 🔹 FOR PYTHON

По умолчание sublime поддерживает дефолтную версию python (2) проверить (создаем файл с асшерением .py) и запускаем `ctrl+b`

```python
import sys
print(sys.version)
```

==изменить==
`tools -> build system -> new build system` сохраняем файл под именем `python3.sublime-build`

в файле сохраняем
```json
{
	"cmd": ["python3", "-i", "-u", "$file"],
	"file_regex": "^[ ]File \"(...?)\", line ([0-9]*)",
	"selector": "source.python"
}
```


==после выбираем==
`tools -> build system -> python3`

---