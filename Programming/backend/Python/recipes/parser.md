#python #recipes 

```python
# многопоточный парсер, запись данных в csv  
# https://coinmarketcap.com/all/views/all/  
# pip install requests  
# pip install lxml  
# pip install multiprocess  
# pip install beautifulsoup4  
  
import requests  
from bs4 import BeautifulSoup  
import csv  
from datetime import datetime  
from multiprocessing import Pool  
  
# получаем странцу с ссылками на криптовалюту  
def get_html(url):  
    # вернет обьект класса Response  
    res = requests.get(url)  
    # возвращаем html страницу  
    return res.text  
  
# получаем все ссылки для криптовалют  
def get_all_links(html):  
    soup = BeautifulSoup(html, 'lxml')  
    tds = soup.find_all('div', class_='cmc-table__column-name')  
    links = []  
  
    for td in tds:  
       a = td.find('a').get('href')  
       link = 'https://coinmarketcap.com' + a  
       links.append(link)  
  
    return links     
  
# парсим стр. криптовалюты  
def get_page_data(html):  
    soup = BeautifulSoup(html, 'lxml')  
    # оборачиваем в блок try,если не найден эелементы устанавливаем пустое значение  
    try:  
       name = soup.find('h1').text.strip()  
    except:  
       name = ''  
  
    try:  
       price = soup.find('span', class_='cmc-details-panel-price__price').text.strip()  
    except:  
       price = ''  
  
    data = {'name': name, 'price': price}  
  
    return data            
  
def write_csv(data):  
    with open('cripto.csv', 'a') as file:  
       writer = csv.writer(file)  
       writer.writerow((data['name'], data['price']))  
       print(data['name'], 'parsed')  
  
def make_all(url):  
    html = get_html(url)  
    data = get_page_data(html)  
    write_csv(data)      
  
  
def main():  
    start = datetime.now()  
  
    url = 'https://coinmarketcap.com/all/views/all/'  
    all_links = get_all_links(get_html(url))  
      
    # парсинг в один процесс  
    # for index, url in enumerate(all_links):    #  html = get_html(url)    #  data = get_page_data(html)    #  write_csv(data)    #  print(index+1, data['name'], 'parsed')  
    # парсим в 40 процессов    with Pool(40) as process:  
       process.map(make_all, all_links)  
  
    end = datetime.now()  
    total = end - start  
    print(str(total))  
  
if __name__ == '__main__':  
    main()
```