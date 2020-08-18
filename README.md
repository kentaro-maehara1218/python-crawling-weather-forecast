# python-crawling-weather-forecast
天気予報サイトをクローリングlineに送る
from urllib.request import urlopen
from urllib.error import HTTPError
from bs4 import BeautifulSoup as soup
import requests

my_url = 'https://tenki.jp/forecast/3/16/4410/13204/'

uClient = urlopen(my_url)
page_html = uClient.read()
uClient.close()
page_soup = soup(page_html, 'html.parser')

title = page_soup.head.title.text
title_for_line = title[:6] + 'の天気' + '\n'
weather = '天気：' + page_soup.find('p', {'class':'weather-telop'}).text + '\n'
temps =  page_soup.find_all('span', {'class':'value'})
max_temp = '最高気温：' + temps[0].text + '℃' + '\n'
min_temp = '最低気温：' + temps[1].text + '℃' + '\n'

#時間のリストを作成
time_list = []
for tr in page_soup.body.tr:
    time_list.append(tr)

for time in time_list:
    time_list.remove('\n')

new_time = []
for time in time_list:
    new_time.append(time.text)

#降水確率のリストを作成
rain_probs = []
for tr in page_soup.find('tr', {'class':'rain-probability'}):
    rain_probs.append(tr)

for rain_prob in rain_probs:
    rain_probs.remove('\n')

new_rain_probs = []

for rain_prob in rain_probs:
    new_rain_probs.append(rain_prob.text)

print(title_for_line)
print(weather)
print(max_temp)
print(min_temp + '\n')
#時間と降水確率を展開
a, b, c, d, e = [(time, rain) for time, rain in zip(new_time, new_rain_probs)]
a1, a2 = a
b1, b2 = b
c1, c2 = c
d1, d2 = d
e1, e2 = e

#"""
line_notify_token = 'line token'
line_notify_api = 'https://notify-api.line.me/api/notify'
headers = {'Authorization': f'Bearer {line_notify_token}'}
data = {'message': title_for_line + weather + max_temp +  min_temp + '\n' + a1 + '    |    ' + a2 + '\n' \
+ b1 + '        ' + b2 + '\n' + c1 + '        ' + c2 + '\n' + d1 + '        ' + d2 + '\n' + e1 + '        ' + e2}
requests.post(line_notify_api, headers = headers, data = data)
