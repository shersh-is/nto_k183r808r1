# Отчет от k183r808r1. Машинка на Винде

### 1 Каким образом вредоносное ПО попало на компьютер пользователя?
Rjomba.exe получен 3-его марта. 3-его марта пользователь проверял свою почту на mail.rumbler.ru. Evgeniy открыл новое входящее письмо с темой «Установка обновлений», сообщающее о необходимости перейти по ссылке. Это было фишинговое письмо с сылкой на скачивание трояна Rjomba.exe.

![win_1](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/4938f4aa-416a-41d5-bd1c-49d9210eaa45)

### 2 С какого сервера была скачана полезная нагрузка?
`http://95.169.192.220:8080/prikol.exe`. Это можно понять по журналу событий на компьютере (Win + R => eventvwr.msc; раздел Windows PowerShell, события 3-его марта)

![win_2](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/5b33694c-1e59-45c4-b4f4-32e4e86b795d)

### 3 С помощью какой уязвимости данное ВПО запустилось? В каком ПО?
`CVE-2023-38831` в WinRar. Версия установленного на компьютере WinRar -- 6.20.0. Данная версия подвержена `CVE-2023-38831` (подвержены все версии WinRAR < 6.23). При данной уязвимости неправильно сравниваются имена файлов, благодаря чему исполняемый код (ВПО) в архиве, который назван также, как и сам передаваемый файл-приманка любого расширения, будет запускаться при разархивировании.

![win_3](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/12328967-eca7-4c31-a83d-64510bba10aa)

Использованный ресурс: `https://habr.com/ru/articles/797127/`

### 4 Какие методы противодействия отладке использует программа?
Программа закрывает большинство опасных для нее приложений при запуске (диспетчер задач, WireShark и т д). Также программа отслеживает, открывается ли она при помощи дебаггера (активен ли отладчик). Если условие выполняется, программа при запуске сразу же закрывается.

### 5 Какой алгоритм шифрования используется при шифровании данных?
AES (CBC mode). Длина ключа - 256 бит. Это можно понять при реверсе вируса с помощью ghidra, благодаря классу CryptoPP::CBC_Encryption и CryptoPP::CBC_ModeBase:

![image](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/82faf5ea-4298-4d99-a72e-26559ff193ae)


### 6 Какой ключ шифрования используется при шифровании данных?
Ответ: `amogusamogusamogusamogusamogusam` (IV `abababababababab`)

Загрузив процесс исполнения Rjomba.exe в `HxD Editor` (запустили от имени администратора) нашли 2 возможных ключа длиной 32 бита: `amogusamogusamogusamogusamogusam` и `sugomasugomasugomasugomasugomasu`. Затем нашли возможный IV длиной 16 бит `abababababababab`.

![win_5](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/8df506a2-ae52-41a5-9f64-718a231de538)

### 7 Куда злоумышленник отсылает собранные данные? Каким образом он аутентифицируется на endpoint?
Злоумышленник отправляет данные себе в Телеграмм, авторизуется при помощи запросов к `api.telegram.org`. Токен `7029575943:AAFNYmmW_QqqMcaHZ-DFRn3M05DptExeAGE`

![Screenshot_2024-03-21_14_38_12(1)](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/9ad09c4f-820c-46c6-8fde-03f7a6853446)
![Screenshot_2024-03-21_14_38_49](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/a13225eb-1e97-4786-a05d-04c65329b5f1)
![Screenshot_2024-03-21_14_39_38](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/96d0c06e-8127-40f6-ae2e-734553144f5d)

### 8 Каково содержимое расшифрованного файла pass.txt на рабочем столе (обновленный файл)?
Ответ: sFYZ#2z9VdUR9sm`3JRz

Использовав decoding from Base64, decoding ранее найденного AES (CBC mode), найденного ключа `amogusamogusamogusamogusamogusam` и IV `abababababababab` получили расшифрованную строчку sFYZ#2z9VdUR9sm`3JRz

![image](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/eee9b46f-d058-4fa8-a6fe-1c1d89b700e7)
