\### Крок 1: Підготовка папки



Створи нову папку для проекту і перейди в неї через термінал:



Bash



```

mkdir my\_wordpress\_site

cd my\_wordpress\_site

```



\### Крок 2: Створення файлу конфігурації 

Створіть файл `docker-compose.yml`, який буде «рецептом» для всього сайту:



Bash



```

touch docker-compose.yml

```



\### Крок 3: Наповнення файлу 



Відкрийте цей файл у текстовому редакторі (наприклад, `nano` або VS Code) і встав туди наступний код, який автор детально розбирав у відео:



YAML



```

version: '3'



services:

&#x20; # 1. Сервіс бази даних

&#x20; db:

&#x20;   image: mysql:5.7

&#x20;   volumes:

&#x20;     - db\_data:/var/lib/mysql

&#x20;   restart: always

&#x20;   environment:

&#x20;     MYSQL\_ROOT\_PASSWORD: password

&#x20;     MYSQL\_DATABASE: wordpress

&#x20;     MYSQL\_USER: wordpress

&#x20;     MYSQL\_PASSWORD: wordpress

&#x20;   networks:

&#x20;     - wp\_site



&#x20; # 2. Сервіс PHPMyAdmin (для керування базою через браузер)

&#x20; phpmyadmin:

&#x20;   depends\_on:

&#x20;     - db

&#x20;   image: phpmyadmin/phpmyadmin

&#x20;   restart: always

&#x20;   ports:

&#x20;     - "8080:80"

&#x20;   environment:

&#x20;     PMA\_HOST: db

&#x20;     MYSQL\_ROOT\_PASSWORD: password

&#x20;   networks:

&#x20;     - wp\_site



&#x20; # 3. Сервіс самого WordPress

&#x20; wordpress:

&#x20;   depends\_on:

&#x20;     - db

&#x20;   image: wordpress:latest

&#x20;   ports:

&#x20;     - "8000:80"

&#x20;   restart: always

&#x20;   volumes:

&#x20;     - ./:/var/www/html

&#x20;   environment:

&#x20;     WORDPRESS\_DB\_HOST: db:3306

&#x20;     WORDPRESS\_DB\_USER: wordpress

&#x20;     WORDPRESS\_DB\_PASSWORD: wordpress

&#x20;     WORDPRESS\_DB\_NAME: wordpress

&#x20;   networks:

&#x20;     - wp\_site



\# Спільні ресурси

volumes:

&#x20; db\_data:



networks:

&#x20; wp\_site:

```



\### Крок 4: Запуск контейнерів



Щоб підняти всю цю структуру, автор вводить лише одну головну команду:



Bash



```

docker-compose up -d

```



\- \*\*`-d`\*\* (detached mode) — означає, що контейнери запустяться у фоновому режимі, і ти зможеш далі користуватися терміналом.

&#x20;   



\### Крок 5: Перевірка роботи



Щоб переконатися, що все запустилося, він використовує:



Bash



```

docker ps

```



Ця команда покаже список активних контейнерів та порти, на яких вони працюють.



\---



\### Як користуватися результатом:



1\. \*\*WordPress:\*\* Відкрий у браузері `localhost:8000`. Там розпочнеться стандартна інсталяція WordPress

&#x20;   

2\. \*\*База даних (PHPMyAdmin):\*\* Відкрий `localhost:8080`.

&#x20;   

&#x20;   - \*\*Користувач:\*\* root

&#x20;       

&#x20;   - \*\*Пароль:\*\* password (як прописано в коді вище)

&#x20;       



\### Ключові фішки цього способу:



\- \*\*Volumes (`./:/var/www/html`):\*\*  Це означає, що як тільки ти запустиш команду, у твоїй папці на комп'ютері автоматично з'являться всі файли WordPress. Ти зможеш їх редагувати прямо зі свого ПК.

&#x20;   

\- \*\*Networks:\*\* Всі сервіси знаходяться в одній мережі `wp\_site`, тому WordPress бачить базу даних просто за іменем `db`, а не за IP-адресою.

&#x20;   



Якщо ти захочеш все видалити, достатньо буде прописати `docker-compose down`.

