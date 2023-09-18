## How to Develop Wordpress in Local Environment and Move it to Live Server in Docker Environment

1.  [Install Docker](https://github.com/mrahman-vf-ic-bd/wordpress-dev-with-docker/blob/main/docker-installation.md)  in the Environment 
    
2.  `uploads.ini` File content looks like following:
    

```
file_uploads = On
memory_limit = 64M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 600
```

3.  Create a docker compose file with following contents

```
version: '3.7'
services:
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    restart: always
    ports:
      - 80:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wp-user
      WORDPRESS_DB_PASSWORD: wp-pass
      WORDPRESS_DB_NAME: wpdb
    volumes:
     #- wordpress:/var/www/html
      - ./wp-content/:/var/www/html/wp-content     
      - ./uploads.ini:/usr/local/etc/php/conf.d/uploads.ini 

  phpmyadmin:
    depends_on:
      - db
    ports:
      - 8080:80
    image: phpmyadmin/phpmyadmin
    environment:
      PMA_HOST: db
      MYSQL_USER: wp-user
      MYSQL_PASSWORD: wp-pass
      MYSQL_ROOT_PASSWORD: rootPassword
      UPLOAD_LIMIT: 500M

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: wpdb
      MYSQL_USER: wp-user
      MYSQL_PASSWORD: wp-pass
      MYSQL_ROOT_PASSWORD: rootPassword
    volumes:
      - db:/var/lib/mysql
    
volumes:
  #wordpress:
  db:
```

4.  At this point activate your Theme and Plugins
5.  Now Let's migrate the database
    Go to `phpmyadmin` of your local wordpress environment and export the database in SQL format
    And import the sql in new environment.
6.  Then run following commands in sql tab in `phpmyadmin` of new environment if the domain name changed.

```
UPDATE wp_posts SET post_content = replace(post_content, 'http://www.local-domain.com', 'http://www.live-domain.com'); 
UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://www.local-domain.com', 'http://www.live-domain.com');
UPDATE wp_posts SET post_content = replace(post_content, 'www.local-domain.com', 'www.live-domain.com'); 
UPDATE wp_postmeta SET meta_value = replace(meta_value,'www.local-domain.com', 'www.live-domain.com');
UPDATE wp_options SET option_value = replace(option_value,'www.local-domain.com', 'www.live-domain.com');
UPDATE wp_posts SET guid = replace(guid, 'www.local-domain.com', 'www.live-domain.com');
```
### To change the schema (http/https) from the site you need to replace the http/https
Following query will replace https --> http
```
UPDATE wp_options SET option_value = replace(option_value,'https://www.local-domain.com', 'http://www.local-domain.com');
```

Website Migration reference: https://jetpack.com/blog/how-to-move-a-wordpress-site-from-localhost-to-a-live-site/

#### Fix file permission to fix upload problem:

Run following command in `wp-content` folder:

```
sudo chown -R www-data /var/www/html/wp-content/uploads
```
