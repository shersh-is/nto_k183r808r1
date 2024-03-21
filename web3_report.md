# Отчет от k183r808r1. Третий вебчик

Дано веб-приложение `http://192.168.12.11:8001/` и исходники к нему:

![Screenshot_2024-03-21_17_54_00](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/a322edf8-476c-4070-bcc0-c346799dacb9)

1. В исходниках в `app.py` видим, что на пути `/flag` можно получить SSTI (используется метод render_template_string), но заходя на /flag, получаем 403 ошибку:

   ![Screenshot_2024-03-21_17_50_31](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/a6757ef0-c375-4d5d-af5b-92cf92020056)

2. Видим в исходниках конфиг к haproxy, который приводит к 403 на /flag:
   
   `acl restricted_page path_beg,url_dec -i /flag`
   
    `http-request deny if restricted_page`

3. Так как в этом конфиге haproxy проверяет, если путь начинается с /flag, и блокирует все где путь так начинается, изменив ссылку чтобы flask отрендерил ее также, но она начиналась не со /flag, мы получим доступ к странице. Обходим это с помощью //flag:

![Screenshot_2024-03-21_17_45_29](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/dd7e7211-5368-47da-a1f9-92c2b711a816)

4. Получив доступ к странице, просто эксплуатируем SSTI. Пейлоад: `{{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat flag.txt').read() }}`

5. Получаем флаг:

![Screenshot_2024-03-21_17_49_53](https://github.com/shersh-is/nto_k183r808r1/assets/81306107/ec2ac413-12bc-4b17-85d4-b9ed12a74262)
