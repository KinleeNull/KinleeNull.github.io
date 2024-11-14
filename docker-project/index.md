# Installing WordPress on Windows through Docker
###### Description: This documentation will walk you through how to install WordPress through Docker on a Windows machine.

- Install docker desktop for Windows through this link:  https://docs.docker.com/desktop/windows/install/

- In a terminal, cd into your Desktop (for simplicity) and make a directory (I called it docker-local-wordpress). Then, open that directory up in Visual Studio Code.

Create a new file in that directory named "docker-compose.yml" and add this information into it:
```shell
version: "3.1"

services: 
  database:
    mem_limit: 2048m
    image: mariadb:10.6.4
    restart: unless-stopped
    ports:
      - 3306:3306
    env_file: .env
    environment:
      MYSQL_ROOT_PASSWORD: '${MYSQL_ROOT_PASSWORD}'
      MYSQL_DATABASE: '${MYSQL_DATABASE}'
      MYSQL_USER: '${MYSQL_USER}'
      MYSQL_PASSWORD: '${MYSQL_PASSWORD}'
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - wordpress-network

  phpmyadmin:
    depends_on:
      - database
    image: phpmyadmin/phpmyadmin
    restart: unless-stopped
    ports:
      - 8081:80
    env_file: .env
    environment:
      PMA_HOST: database
      MYSQL_ROOT_PASSWORD: '${MYSQL_ROOT_PASSWORD}'
    networks:
      - wordpress-network

  wordpress:
    depends_on:
      - database
    image: wordpress:6.2.2-apache
    restart: unless-stopped
    ports:
      - 8080:80
    env_file: .env
    environment:
      WORDPRESS_DB_HOST: database:3306
      WORDPRESS_DB_NAME: '${MYSQL_DATABASE}'
      WORDPRESS_DB_USER: '${MYSQL_USER}'
      WORDPRESS_DB_PASSWORD: '${MYSQL_PASSWORD}'
    volumes:
      - ./:/var/www/html
    networks:
      - wordpress-network

volumes:
  db-data:

networks:
  wordpress-network:
    driver: bridge
```
You'll then create another file in that directory named ".env" and add this information to it:  
```shell
MYSQL_DATABASE=wordpress
MYSQL_USER=wp_user  # you can change the user and password, as it will be used later
MYSQL_PASSWORD=wp_password
MYSQL_ROOT_PASSWORD=root_password
```

Then, in your terminal, cd into that directory you made and run:
```shell
docker compose up
```
It will run for a minute, but you'll know it's complete when you see this in your terminal, a few lines up from the bottom:
![Screenshot 2024-11-13 201723](https://github.com/user-attachments/assets/cf9fbdea-9255-4546-9f18-c054be8036c6)

- Once you see that, go to a web browser and in the searchbar, type in ```localhost:8080```.

- This should take you to the WordPress image, where you'll choose your language, provide a site name, username, password, and email. Then click ```Install WordPress```.

- Proceed to login with the credentials you just created and you'll be brought to the WordPress Admin Dashboard!

- From your dashboard, you can edit posts and customize your site.

- You can also go to ```localhost:8081``` in your browser and look at the phpMyAdmin. The login credentials will be those created in the .env file.

To stop running the docker container: in a terminal, cd into the wordpress directory created earlier and run this command:
```shell
docker compose down
```
##### I used this video as a resource and it gives a good walkthrough of the process: https://youtu.be/gEceSAJI_3s?si=2HMM_ctKBD-DyOhQ
