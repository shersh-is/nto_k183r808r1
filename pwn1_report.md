# Отчет от k183r808r1. Первый пывн

1. Заметим функцию `win`, нужно вызвать ее.

![Screenshot_2024-03-21_16_55_07](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/e120e8d7-4f55-41e8-9f0e-f96e23188a2f)

2. Заметим, что `printf` без ограничений, поэтому в нем есть уязвиомсть форматной строки.

3. Теперь перезапишем адрес функции `exit` на адрес функции `win` в `GOT` таблице. Таким образом адрес функции `exit` - `0x404018`, а функции `win` - `0x401156`.

![Screenshot_2024-03-21_16_00_57](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/5e54eed3-494c-4e34-a939-7c09bef956f9)
![Screenshot_2024-03-21_16_01_02](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/57d50f94-0eea-41fb-8034-55ae91c74a02)

4. Далее выполнеяем перезапись адреса по статье: https://habr.com/ru/articles/460647/. В нашем случае уязвимость обсалютно та же.
  
5. Разбиваем адрес функции `win` на 2 части (`0x4011` и `0x56`), и записываем их по адресам: `0x404018` и `0x404019`.

6. Теперь когда в `GOT` таблице на месте адреса функции `exit` лежит адрес функции `win`, вместо выхода из программа исполняется запуск шелла.

![Screenshot_2024-03-21_16_01_11](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/9e7dafc2-b546-4d72-8216-f313abf60402)

7. Затем получаем список файлов (`ls`), и получаем флаг (`cat flag`)