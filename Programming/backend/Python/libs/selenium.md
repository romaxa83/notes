#python #package

Selenium - это мощный инструмент для автоматизации действий веб-браузера. Он позволяет программно управлять браузером, открывать веб-страницы, заполнять формы, кликать. Инструмент состоит из разных модулей: Selenium IDE, Selenium Grid, Selenium Standalone Server і Selenium WebDriver

```bash
pip install selenium
```

Для работы с Selenium необходим WebDriver - это программный ин­струмент, который обеспечивает взаимодействие между вашим кодом на Python и конкретным браузером. Вам нужно будет скачать и устано­вить соответствующий WebDriver для выбранного браузера (например, Chrome, Firefox, Safari). Затем нужно указать путь к драйверу в вашем коде Selenium. После настройки WebDriver вы можете начать использовать Selenium для автоматизации браузера.

установка driver для мака
```bash
brew install --cask chromedriver
```

>Если на Mac возникла ошибка безопасности
>Если при первом запуске macOS заблокирует драйвер:
1. Зайдите в **Системные настройки** > **Конфиденциальность и безопасность**.
2. Прокрутите вниз до раздела «Безопасность», там появится сообщение о блокировке `chromedriver`. Нажмите **«Подтвердить вход»** (Allow Anyway

##### 🔸 Настройки браузер

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

options = Options()
options.add_argument("--headless")  # Фоновый режим без интерфейса
options.add_argument("--no-sandbox")  # Для Linux
options.add_argument("--disable-dev-shm-usage")  # Экономия памяти
options.add_argument("--start-maximized")  # Развернуть окно на весь экран

# создание драйвера
driver = webdriver.Chrome(options=options)

# Открытие сайта
driver.get("https://example.com")
```

 ==Внешний вид и размер окна==
- `--start-maximized` — запускает браузер сразу в развернутом на весь экран виде.
- `--window-size=1920,1080` — устанавливает конкретное разрешение окна (особенно важно для `--headless`, чтобы элементы не «съезжали»).
- `--incognito` — режим инкогнито (не сохраняются куки и история).

==Отключение ненужных функций (ускорение)==
- `--disable-notifications` — отключает всплывающие запросы на отправку уведомлений.
- `--disable-extensions` — отключает все расширения браузера для экономии ресурсов.
- `--disable-popup-blocking` — отключает блокировку всплывающих окон.
- `--disable-gpu` — отключает графическое ускорение (часто используется вместе с headless для стабильности на серверах).
- `--mute-audio` — отключает звук в браузере.

==Скрытность (от детектирования ботов)==
- `--disable-blink-features=AutomationControlled` — **самый важный флаг**: он убирает заголовок `navigator.webdriver`, по которому сайты мгновенно понимают, что зашел бот.
- `user-agent=Ваша_Строка` — позволяет подменить данные о браузере и системе (User-Agent), чтобы выглядеть как обычный пользователь

==Работа с профилем и данными==
- `user-data-dir=/путь/к/профилю` — позволяет использовать ваш реальный профиль Chrome (со всеми паролями, куками и закладками).
- `--remote-debugging-port=9222` — открывает порт для удаленной отладки.


##### 🔸 Базовые методы работы с браузером

==Пример открытия браузера==
```python
from selenium import webdriver
    
# Запуск браузера (например, Chrome)
driver = webdriver.Chrome('/path/to/chromedriver')
    
# Загрузка веб-страницы 
driver.get('https://www.example.com')
```

```python
# Получение информации о странице
title = driver.title  # Заголовок страницы
url = driver.current_url  # Текущий URL
page_source = driver.page_source  # HTML-код страницы

# Навигация
driver.back()  # Назад
driver.forward()  # Вперед
driver.refresh()  # Обновить страницу

# Управление окном
driver.maximize_window()  # Развернуть на весь экран
driver.set_window_size(1920, 1080)  # Установить размер

# Закрытие
driver.quit()  # Закрыть браузер полностью
driver.close()  # Закрыть текущую вкладку
```

##### 🔸 Поиск элементов на странице
```python
from selenium.webdriver.common.by import By

# Поиск одного элемента
element = driver.find_element(By.ID, "element_id")
element = driver.find_element(By.NAME, "element_name")
element = driver.find_element(By.CLASS_NAME, "class_name")
element = driver.find_element(By.TAG_NAME, "div")
element = driver.find_element(By.CSS_SELECTOR, ".container > .button")
element = driver.find_element(By.XPATH, "//button[@class='submit']")
element = driver.find_element(By.LINK_TEXT, "Нажми меня")
element = driver.find_element(By.PARTIAL_LINK_TEXT, "Нажми")

# Поиск нескольких элементов
elements = driver.find_elements(By.CLASS_NAME, "item")  # Возвращает список
for elem in elements:
    print(elem.text)
```

==или так==
```python
# Поиск элемента по id
element = driver.find_element_by_id('username')
   
# Поиск элемента по имени класса  
elements = driver.find_elements_by_class name('btn-primary')
```

##### 🔸 Получение информации из элементов
Selenium также позволяет извлекать данные из веб-страниц, например­ текст элемента или значение атрибута.

```python
python
element = driver.find_element(By.ID, "info")

# Текст элемента
text = element.text
print(f"Текст: {text}")

# Атрибуты элемента
href = element.get_attribute("href")
data_id = element.get_attribute("data-id")
class_name = element.get_attribute("class")

# Значение (для input, select и т.д.)
value = element.get_attribute("value")

# CSS-свойства
color = element.value_of_css_property("color")
font_size = element.value_of_css_property("font-size")

# Проверка видимости и включенности
is_displayed = element.is_displayed()
is_enabled = element.is_enabled()
is_selected = element.is_selected()  # Для чекбоксов и радио-кнопок

# Размер и позиция
size = element.size  # {'height': 20, 'width': 100}
location = element.location  # {'x': 10, 'y': 20}
rect = element.rect  # {'x': 10, 'y': 20, 'width': 100, 'height': 20}
```

##### 🔸 Ищем элементы только в контексте найденного элемента
```python
container = driver.find_element(By.ID, "container")
items = container.find_elements(By.CLASS_NAME, "item")
```

##### 🔸 Взаимодействие с элементами

==клик и отправка текста==
```python
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.keys import Keys

button = driver.find_element(By.ID, "submit_btn")

# Простой клик
button.click()

# Клик с задержкой
import time
time.sleep(2)
button.click()

# Двойной клик
actions = ActionChains(driver)
actions.double_click(button).perform()

# Правый клик
actions.context_click(button).perform()

# Ввод текста
input_field = driver.find_element(By.ID, "search")
input_field.send_keys("Hello World")
input_field.send_keys(Keys.ENTER)  # Нажать Enter
input_field.send_keys(Keys.CONTROL + "a")  # Выделить все
input_field.send_keys(Keys.DELETE)  # Удалить

# Очистить поле
input_field.clear()
```

==Прокрутка страницы==
```python
# Прокрутить вниз на определенное количество пикселей
driver.execute_script("window.scrollBy(0, 500);")

# Прокрутить до конца страницы
driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")

# Прокрутить элемент в видимую область
element = driver.find_element(By.ID, "target")
driver.execute_script("arguments[0].scrollIntoView(true);", element)
```

##### 🔸 Ожидания (Waits)
Это критически важная часть для работы с динамическим контентом. Иногда вам может потребоваться подождать появления или исчезновения элемента на странице. Для этого вы можете использовать различные методы ожидания:
```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException

# Явное ожидание (рекомендуется)
wait = WebDriverWait(driver, 10)  # Ждет максимум 10 секунд

# Ждем, пока элемент появится и будет кликабелен
element = wait.until(
    EC.element_to_be_clickable((By.ID, "submit_btn"))
)
element.click()

# Ждем видимости элемента
element = wait.until(
    EC.visibility_of_element_located((By.CLASS_NAME, "notification"))
)

# Ждем наличия элемента в DOM
element = wait.until(
    EC.presence_of_element_located((By.XPATH, "//div[@data-id='123']"))
)

# Ждем исчезновения элемента
wait.until(
    EC.invisibility_of_element_located((By.CLASS_NAME, "loader"))
)

# Неявное ожидание (менее рекомендуемо)
driver.implicitly_wait(10)  # Ждет 10 сек при поиске элементов
```


```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as ЕС

# Ожидание появления элемента
element = WebDriverWait(driver, 10).until(EC.presence_of_element located((By.ID, 'username')))
```

##### 🔸 Работа с формами
```python
# Выбор из выпадающего списка
from selenium.webdriver.support.select import Select

select_element = driver.find_element(By.ID, "country")
select = Select(select_element)

# Выбор по видимому тексту
select.select_by_visible_text("Россия")

# Выбор по значению атрибута
select.select_by_value("ru")

# Выбор по индексу
select.select_by_index(0)

# Получить все опции
all_options = select.options
for option in all_options:
    print(option.text)

# Получить выбранный элемент
selected_option = select.first_selected_option
print(selected_option.text)

# Работа с чекбоксом
checkbox = driver.find_element(By.ID, "agree")
if not checkbox.is_selected():
    checkbox.click()  # Отметить, если не отмечен

# Отправка формы
form = driver.find_element(By.ID, "my_form")
form.submit()
```

#### 🔹 Работа с JavaScript

```python
# Выполнить JavaScript код
result = driver.execute_script("return document.title;")
print(result)

# Передать параметры
element = driver.find_element(By.ID, "test")
driver.execute_script("arguments[0].style.border='3px solid red';", element)

# Получить данные из локального хранилища
local_storage_data = driver.execute_script(
    "return window.localStorage.getItem('key');"
)

# Проверить наличие элемента через JS
exists = driver.execute_script(
    "return document.querySelector('.dynamic-element') !== null;"
)
```

#### 🔹 Работа с окнами и вкладками

```python
# Получить список всех вкладок
window_handles = driver.window_handles
print(f"Всего вкладок: {len(window_handles)}")

# Переключиться на другую вкладку
driver.switch_to.window(window_handles[1])

# Закрыть текущую вкладку и вернуться к первой
driver.close()
driver.switch_to.window(window_handles[0])

# Работа с iframe
iframe = driver.find_element(By.TAG_NAME, "iframe")
driver.switch_to.frame(iframe)  # Перейти в iframe
element_in_iframe = driver.find_element(By.ID, "element_id")
driver.switch_to.default_content()  # Вернуться в основной контент
```

#### 🔹 Обработка исключений

```python
from selenium.common.exceptions import (
    NoSuchElementException,
    TimeoutException,
    ElementNotInteractableException,
    StaleElementReferenceException
)

try:
    element = driver.find_element(By.ID, "nonexistent")
except NoSuchElementException:
    print("Элемент не найден")

try:
    wait.until(EC.visibility_of_element_located((By.ID, "element")))
except TimeoutException:
    print("Элемент не появился за отведенное время")

try:
    element.click()
except ElementNotInteractableException:
    print("Элемент не интерактивен (скрыт, заблокирован и т.д.)")
```
#### 🔹 Простые примеры

- Предположим, у вас есть веб-страница с формой для входа, и вы хотите автоматически заполнить эту форму и войти на сайт:
```python
from selenium import webdriver

# Запуск браузера
driver = webdriver.Chrome('/path/to/chromedriver')

# Загрузка страницы для входа 
driver.get('https://example.com/login')

# Находим поля ввода и кнопку входа  
username_input = driver.find_element by_id('username') 
password_input = driver.find_element_by_id('password') 
login_button = driver.find_element_by_id('login_button')

# Вводим данные и нажимаем кнопку входа 
username_input.send_keys('my_username') 
password_input.send_keys('my_password')
login_button.click()
```

- Предположим, вы хотите автоматически прокрутить страницу вниз и собрать информацию о пользователях с веб-страницы социальной сети:
```python
from selenium import webdriver  
import time 
 
# Запуск браузера  
driver = webdriver.Chrome('/path/to/chromedriver') 

# Загрузка страницы 
driver.get('https://example.com/users')

# Прокрутка страницы вниз
for i in range(З):
	driver.execute_script("window.scrollTo(O, document.body. scrollHeight);")
	# Пауза для загрузки дополнительных данных 
	time.sleep(2) 
 
# Извлечение данных о пользователях 
users = driver.find_elements_by_class_name('user-info')  
for user in users:
	print(user.text)
```

- Предположим, у вас есть неб-приложение, где вы хотите автоматически создавать новые записи
```python
from selenium import webdriver

# Запуск браузера  
driver = webdriver.Chrome('/path/to/chromedriver')

# Загрузка страницы веб-приложения 
driver.get('https://example.com/new_post')

# Находим поля ввода для заголовка и содержимого записи 
title_input = driver.find_element_by_id('title') 
content_input = driver.find_element_by_id('content') 
submit_button = driver.find_element_by_id('submit')

# Вводим данные и отправляем форму 
title_input.send_keys('Hoвaя запись') 
content_input.send_keys('Coдepжимoe новой записи') 
submit_button.click()
```

- Предположим, у вас есть веб-приложение, и вы хотите автоматически протестировать его функциональность:
```python
import unittest  
from selenium import webdriver

class TestApp(unittest.TestCase):
	def setUp(self):  
		self.driver webdriver.Chrome('/path/to/ chromedriver')

	def test_login(self): 
		self.driver.get('https://example.com/login') 
	# Здесь проводите тестирование входа на сайт
	
	def test_create_post(self):
		self.driver. get( .' https: //exarnple. corn/new_post')  
		# Здесь проводите тестирование создания новой записи
		
	def tearDown(self): 
		self.driver.quit()
		
if __name__ == "__main__":
	unittest.rnain()
```

- Веб-скрепинг динамического сайта
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import csv

driver = webdriver.Chrome()
wait = WebDriverWait(driver, 10)

try:
    driver.get("https://example-ecommerce.com")
    
    # Ожидаем загрузки товаров (динамический контент)
    wait.until(EC.presence_of_all_elements_located(
        (By.CLASS_NAME, "product-item")
    ))
    
    products = []
    for item in driver.find_elements(By.CLASS_NAME, "product-item"):
        name = item.find_element(By.CLASS_NAME, "product-name").text
        price = item.find_element(By.CLASS_NAME, "product-price").text
        products.append({"name": name, "price": price})
    
    # Сохраняем в CSV
    with open("products.csv", "w", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=["name", "price"])
        writer.writeheader()
        writer.writerows(products)
        
finally:
    driver.quit()
```

- Автоматический вход и парсинг личного кабинета
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()

try:
    driver.get("https://example.com/login")
    
    # Вход
    email_input = driver.find_element(By.ID, "email")
    password_input = driver.find_element(By.ID, "password")
    
    email_input.send_keys("user@example.com")
    password_input.send_keys("password123")
    password_input.send_keys(Keys.ENTER)
    
    # Ожидаем загрузки страницы профиля
    wait = WebDriverWait(driver, 10)
    wait.until(EC.presence_of_element_located(
        (By.CLASS_NAME, "user-profile")
    ))
    
    # Извлекаем информацию
    profile_name = driver.find_element(By.CLASS_NAME, "user-name").text
    orders = driver.find_elements(By.CLASS_NAME, "order-item")
    
    print(f"Профиль: {profile_name}")
    print(f"Количество заказов: {len(orders)}")
    
finally:
    driver.quit()
```

- Тестирование веб-приложения
```python
import unittest
from selenium import webdriver
from selenium.webdriver.common.by import By

class TestLoginPage(unittest.TestCase):
    
    def setUp(self):
        self.driver = webdriver.Chrome()
        self.driver.get("https://example.com/login")
    
    def tearDown(self):
        self.driver.quit()
    
    def test_successful_login(self):
        email = self.driver.find_element(By.ID, "email")
        password = self.driver.find_element(By.ID, "password")
        submit_btn = self.driver.find_element(By.ID, "submit")
        
        email.send_keys("user@example.com")
        password.send_keys("password123")
        submit_btn.click()
        
        # Проверяем, что мы на странице профиля
        self.assertIn("profile", self.driver.current_url)
        
    def test_invalid_email(self):
        email = self.driver.find_element(By.ID, "email")
        email.send_keys("invalid-email")
        
        error_msg = self.driver.find_element(By.CLASS_NAME, "error")
        self.assertTrue(error_msg.is_displayed())

if __name__ == "__main__":
    unittest.main()
```

- Заполнение и отправка форм
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.select import Select

driver = webdriver.Chrome()
wait = WebDriverWait(driver, 10)

driver.get("https://example.com/survey")

try:
    # Текстовые поля
    driver.find_element(By.ID, "name").send_keys("Иван Петров")
    driver.find_element(By.ID, "email").send_keys("ivan@example.com")
    
    # Выпадающий список
    country_select = Select(driver.find_element(By.ID, "country"))
    country_select.select_by_visible_text("Россия")
    
    # Текстовая область
    driver.find_element(By.ID, "comments").send_keys(
        "Ваше предложение очень хорошее!"
    )
    
    # Чекбоксы
    agree_checkbox = driver.find_element(By.ID, "agree")
    if not agree_checkbox.is_selected():
        agree_checkbox.click()
    
    # Отправка формы
    driver.find_element(By.ID, "submit").click()
    
    # Проверяем успешную отправку
    success_msg = wait.until(
        EC.visibility_of_element_located((By.CLASS_NAME, "success-message"))
    )
    print(f"Сообщение: {success_msg.text}")
    
finally:
    driver.quit()
```

- Работа с данными, требующими подождать
```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Chrome()
wait = WebDriverWait(driver, 15)

driver.get("https://example.com/data-table")

try:
    # Ждем, пока таблица загрузится и станет видна
    table = wait.until(
        EC.visibility_of_element_located((By.ID, "data-table"))
    )
    
    # Ждем загрузки строк
    rows = wait.until(
        EC.presence_of_all_elements_located((By.TAG_NAME, "tr"))
    )
    
    # Обрабатываем данные
    for row in rows[1:]:  # Пропускаем заголовок
        cells = row.find_elements(By.TAG_NAME, "td")
        print(f"{cells[0].text} - {cells[1].text}")
        
finally:
    driver.quit()
```

#### 🔹 Лучшие практики

- Всегда используйте явные ожидания вместо `time.sleep()`
- Закрывайте браузер с помощью `driver.quit()` в блоке `finally`
- Обрабатывайте исключения — веб-тестирование непредсказуемо
- Используйте `headless` режим для повышения производительности
- Добавляйте уникальные селекторы (ID, data-атрибуты) к элементам в тестируемом коде
- Переиспользуйте WebDriverWait вместо создания нового для каждого ожидания
- Проверяйте видимость и интерактивность перед кликом на элемент