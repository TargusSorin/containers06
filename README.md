### Pașii de lucru
1) Am creat directorul containers06 și în el am creat directorul mounts/site. În acest director am copiat un site de la perechile de PHP.
1) În directorul rădăcină am creat un fișier .gitignore cu următorul conținut:
```
# Ignore files and directories
mounts/site/*
```
3) În directorul rădăcină am creat un director cu numele nginx în care am creat un fișier default.conf cu următorul conținut:
```
server {
    listen 80;
    server_name _;
    root /var/www/html;
    index index.php;
    location / {
        try_files $uri $uri/ /index.php?$args;
    }
    location ~ \.php$ {
        fastcgi_pass backend:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```
4) 