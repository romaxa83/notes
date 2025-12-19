

При написании скриптов в начале файла желательно указывать интерпретатор
```bash
#!/usr/bin/env python3
```
запустить такой скрипт можно `chmod +x hello.py` (если файлу были предоставлены разрешения выполнения).


интерпретатор находиться по пути /usr/lib
чтоб узнать в интерактивной оболочки вводим:
```python
>>>import sys
>>>sys.path
```


---
http://dev-lab.info/2019/03/%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0-%D1%81-rabbitmq-%D0%B2-python-%D1%81-%D1%87%D0%B5%D0%B3%D0%BE-%D0%BD%D0%B0%D1%87%D0%B0%D1%82%D1%8C/

https://pythonworld.ru/gui/pyqt5-firstprograms.html

https://python-visualization.github.io/folium/flask.html

Блог по python

https://andreyex.ru/ubuntu/kak-ustanovit-python-3-7-v-ubuntu-18-04/
-------------------------------------------------------
https://pythonworld.ru/gui/pyqt5-firstprograms.html
---------------------------------------------------------
https://tproger.ru/translations/an-a-z-of-python-tricks/
--------------
Анализ автоаварий в Барселоне с
использованием Pandas, Matplotlib и Folium

https://nuancesprog.ru/p/4868
------------------------------------------------------
20 фрагментов Python, которые стоит выучить прямо сегодня
https://nuancesprog.ru/p/5604/
------------------------------------------------------
Разработка надёжных Python-скриптов
https://habr.com/ru/company/ruvds/blog/462007/
-----------------------------------------------------
Контейнеризация в Python
https://nuancesprog.ru/p/9384/
------------------------------------------------------
Пять действительно крутых пакетов Python
https://nuancesprog.ru/p/9355/
-----------------------------------------------------
========
OpenCV

https://habr.com/ru/post/504458/

https://habr.com/ru/post/358902/

---------------------------------------------------
Обнаружение лица и выделение характерных точек
https://api-2d3d-cad.com/python-face-detection/#3
--------------------------------------------------
Параллельная обработка видео с помощью OpenCV

https://proglib.io/p/parallelnaya-obrabotka-video-s-pomoshchyu-opencv-2020-09-05

создаем виртульную среду и активируем ее
- pip install opencv-python
- pip install matplotlib
- pip install jupyterlab
запускаем jupyter notebook
В открывшемся окне создаем новый Python 3 файл, и запускаем команду:
import cv2 as cv
print( cv.__version__ )

-------------------------------------------
Получить изображение с web камеры

import numpy as np
import cv2


cap = cv2.VideoCapture(0)

while(True): 
    ret, frame = cap.read()
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    cv2.imshow('Video', frame)
    # cv2.imshow('frame',gray)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()

-------------------------------------------
Вывести изображение на экран (выход любая клавиша)

image = cv2.imread("./путь/к/изображению.расширение")
cv2.imshow("Image", image)
cv2.waitKey(0)
cv2.destroyAllWindows()
=======