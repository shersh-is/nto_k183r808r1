# Отчет от k183r808r1. Второй вебчик

Дано веб-приложение и `.jar` исходный файл

![Screenshot_2024-03-21_16_31_36](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/18d3335d-d0a3-4449-8a43-95a984ff4d8a)

1. Декомпилируем `.jar` файл с помощью `jadx-gui`, и видим, что приложение использует фреймворк для веб разработки на `Java` `Spring`.

![Screenshot_2024-03-21_16_32_46](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/1041641c-7597-44a8-bac5-647f762643a2)

2. Видим путь `/doc/{document}` в классе, отвечающем за обработку ответов веб-сервера.

![Screenshot_2024-03-21_16_35_54](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/181a70f3-6c3f-4ec5-aa1d-b7fa4ade0353)

4. При попытке сделать запрос на этот путь выводится ошибка, и в этой ошибке экранируется часть ссылки (`doc/{document}`).

![Screenshot_2024-03-21_16_37_12](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/9088fd7e-1f34-4d45-8e1f-e81855bab10a)

5. Эксплуатируем SSTI для Spring следующим пейлоадом: `__${new java.util.Scanner(T(java.lang.Runtime).getRuntime().exec("cat password.txt").getInputStream()).next()}__::.x__${T(java.lang.Runtime).getRuntime().exec("touch executed")}__::.x`

![Screenshot_2024-03-21_16_48_00](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/f5d958e8-b3ce-4bef-aa02-9cbbe3cc6224)

и получаем пароль `33a61c66899af2114c8f98d80ceb2857`.

Использованный ресурс: https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection#spring-view-manipulation-java

7. Подставляем полученный пароль в `/login?pass=` и получаем флаг.

![Screenshot_2024-03-21_16_46_00](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/442eeb5f-0d0e-4e4b-bb65-d5755d87738b)
