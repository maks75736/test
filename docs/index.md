### Крок 1: Підготовка папки

Створи нову папку для проекту і перейди в неї через термінал:

Bash

```
mkdir my_wordpress_site
cd my_wordpress_site
```

### Крок 2: Створення файлу конфігурації 
Створіть файл `docker-compose.yml`, який буде «рецептом» для всього сайту:

Bash

```
touch docker-compose.yml
```

### Крок 3: Наповнення файлу 

Відкрийте цей файл у текстовому редакторі (наприклад, `nano` або VS Code) і встав туди наступний код, який автор детально розбирав у відео:

YAML

```
version: '3'

services:
  # 1. Сервіс бази даних
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    networks:
      - wp_site

  # 2. Сервіс PHPMyAdmin (для керування базою через браузер)
  phpmyadmin:
    depends_on:
      - db
    image: phpmyadmin/phpmyadmin
    restart: always
    ports:
      - "8080:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: password
    networks:
      - wp_site

  # 3. Сервіс самого WordPress
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - "8000:80"
    restart: always
    volumes:
      - ./:/var/www/html
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    networks:
      - wp_site

# Спільні ресурси
volumes:
  db_data:

networks:
  wp_site:
```

### Крок 4: Запуск контейнерів

Щоб підняти всю цю структуру, автор вводить лише одну головну команду:

Bash

```
docker-compose up -d
```

- **`-d`** (detached mode) — означає, що контейнери запустяться у фоновому режимі, і ти зможеш далі користуватися терміналом.
    

### Крок 5: Перевірка роботи

Щоб переконатися, що все запустилося, він використовує:

Bash

```
docker ps
```

Ця команда покаже список активних контейнерів та порти, на яких вони працюють.

---

### Як користуватися результатом:

1. **WordPress:** Відкрий у браузері `localhost:8000`. Там розпочнеться стандартна інсталяція WordPress
    
2. **База даних (PHPMyAdmin):** Відкрий `localhost:8080`.
    
    - **Користувач:** root
        
    - **Пароль:** password (як прописано в коді вище)
        

### Ключові фішки цього способу:

- **Volumes (`./:/var/www/html`):**  Це означає, що як тільки ти запустиш команду, у твоїй папці на комп'ютері автоматично з'являться всі файли WordPress. Ти зможеш їх редагувати прямо зі свого ПК.
    
- **Networks:** Всі сервіси знаходяться в одній мережі `wp_site`, тому WordPress бачить базу даних просто за іменем `db`, а не за IP-адресою.
    

Якщо ти захочеш все видалити, достатньо буде прописати `docker-compose down`.