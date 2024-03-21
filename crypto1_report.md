 # Отчет от k183r808r1. Первая крипта
1. Открыли исходный код, проанализировали и протестировали функционал ресурсов `/api/EncryptPin`, `/api/EncryptedPin`, `'/api/Time'`, `'/api/CheckPin'`.

2. Заметили, что `/api/CheckPin` позволяет делать 1 запрос в минуту. Выяснили, что возможно 2 решения: через криптографию и через брутфорс пин-кода.

3. Решили попробовать применить брутфорс, перебрав все возможные значения pin'а. Для этого мы запрашивали текущий `EncryptedPin` и заправшивали `EncryptPin` от нашего пина. Сравнивали полученные значения, и заметили совпадающие значения при `pin = 3561`.

4. Проверили через `/api/CheckPin` `pin = 3561`, получили флаг.
---
### Исходный код
``` python
import requests
import json
import threading
import time

url = "http://192.168.12.12:5000/api/CheckPin"

def send_request(pin):
    
    burp0_url = "http://192.168.12.12:5000/api/EncryptPin"
    burp0_headers = {"Cache-Control": "max-age=0", "Upgrade-Insecure-Requests": "1", "Content-Type": "application/json", "Accept": "application/json", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.6167.85 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", "Accept-Encoding": "gzip, deflate, br", "Accept-Language": "en-US,en;q=0.9", "Connection": "close"}
    burp0_json={"pin": pin}
    response = requests.post(burp0_url, headers=burp0_headers, json=burp0_json)
    
    burp0_url = "http://192.168.12.12:5000/api/EncryptedPin"
    burp0_headers = {"Cache-Control": "max-age=0", "Upgrade-Insecure-Requests": "1", "Content-Type": "application/json", "Accept": "application/json", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.6167.85 Safari/537.36", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", "Accept-Encoding": "gzip, deflate, br", "Accept-Language": "en-US,en;q=0.9", "Connection": "close"}
    response2 = requests.get(burp0_url, headers=burp0_headers, json=burp0_json)

    print(response.text.split('"')[-2], response2.text.split('"')[-2], pin)
    if (response.text.split('"')[-2] == response2.text.split('"')[-2]):
        print(pin)
        exit(0)


threads = []
for pin in range(0, 10**16):
    thread = threading.Thread(target=send_request, args=(pin,))
    threads.append(thread)
    thread.start()
    time.sleep(0.01)

for thread in threads:
    thread.join()
```
