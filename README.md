### Numele lucrării de laborator
Interacțiunea containerelor

### Scopul lucrării
Învățarea gestionării interacțiunii dintre mai multe containere.

### Sarcina
Crearea unei aplicații PHP pe baza a două containere: nginx, php-fpm

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
4) Am creat rețeaua **internal** cu următoarea comandă:
```
docker network create internal
```
5) Am creat container de backend de la imaginea php:7.4-fpm, cu volumele din D:/containers06/mounts/site, în rețeaua **internal** cu comanda:
```
docker run -d --name backend --network internal -v D:/containers06/mounts/site:/var/www/html php:7.4-fpm
```
6) Am creat un container cu următoarele proprietăți: creat în baza imaginii nginx:1.23-alpine, directorul mounts/site este montat în /var/www/html, fișierul nginx/default.conf este montat în /etc/nginx/conf.d/default.conf, portul 80 al containerului este expus pe portul 80 al calculatorului gazdei și este creat în rețeaua **internal**:
```
docker run -d --name frontend --network internal -p 80:80 -v D:/containers06/mounts/site:/var/www/html -v D:/containers06/nginx/default.conf:/etc/nginx/conf.d/default.conf nginx:1.23-alpine
```
7) Când am accesat adresa **http://localhost** în browser a apărut site-ul creat la orele de PHP:
![image](./image/Screenshot%202025-04-06%20113046.png)

#### Întrebări:
1. În ce mod în acest exemplu containerele pot interacționa unul cu celălalt?
Containerele interacționează între ele prin faptul că fac parte din aceeași rețea și prin volumul montat comun.
2. Cum văd containerele unul pe celălalt în cadrul rețelei internal?
Făcând parte din aceeași rețea, containerele se văd în baza numelui fiecăruia.
3. De ce a fost necesar să se suprascrie configurarea nginx?
Configurația nginx a fost necesară să fie suprascrisă pentru a putea executa conținut PHP, nu doar conținut static, făcând legătura cu imaginea PHP-fpm.

### Concluzie:
În cadrul acestei lucrări de laborator, am studiat interacțiunea între containerele Docker care fac parte din aceeași rețea și care au un volum montat comun. Am văzut cum un container bazat pe imaginea serverului nginx comunică cu un container bazat pe imaginea PHP-fpm, facilitând procesarea scripturilor php. Astfel, am învățat cum o rețea de containere permite rularea unui site cu php pe un anumit port. 
