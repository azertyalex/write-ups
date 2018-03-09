# Cyber Security Challenge Belgium: water-you-talking-about-50

**Category:** Web Security
**Points:** 50
**Solves:** 15
**Description:**

> (missing description)

## Write-up

I do not have the description anymore but it said something along the lines of *The flag is a russian word lost in translation*

![index.php]( https://raw.githubusercontent.com/azertyalex/write-ups/master/CSCBE-2018/water-you-talking-about-50/index.jpg "index.php")

When visiting the website we are greeted by a modern design of a Hydropump 2.1 installation. Because of the description I immediatly start browsing looking for a russian word which did not get translated. Nothing turns up for a while so I click on `Pусский` to get the russian version of the site. Still nothing... Time to dig a little deeper!

```
GET / HTTP/1.1
Host: **.***.***.***
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:58.0) Gecko/20100101 Firefox/58.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Cookie: lang=en; ci_session=lmqa66bn85is3ruuls9301um2ivs86tq
Connection: close
Upgrade-Insecure-Requests: 1
```

I look at the request in Burp Suite and try a few basic tricks, still nothing...
However, when fiddling around with the language cookie I noticed some php errors turned up! Aha! We have some file inclusion going on here!

![php_error]( https://raw.githubusercontent.com/azertyalex/write-ups/master/CSCBE-2018/water-you-talking-about-50/php_error.jpg "php_error")

Trying to get /etc/passwd will not work because .php is appended and trying to use string terminators like %00 won't work, we have to try something else here. I tried to include remote files by specifying a URL as parameter, sadly allow_url_include was turned off so that failed.
PHP has a thing called filter, via this command we can use `php://filter/convert.base64-encode/resource=index` as a payload to get code execution in our lang cookie to get the php sourcecode of the index page in a base64 string. Now it was simply finding the right file that contains the flag and surely enough `php://filter/convert.base64-encode/resource=ru` gave us the flag!

![ru.php in base64]( https://raw.githubusercontent.com/azertyalex/write-ups/master/CSCBE-2018/water-you-talking-about-50/ru.jpg "ru.php in base64")

**Decoded**:

```
<?php
$footer = "&copy; Secure Operational Systems (SoS) 2017";

$welcome = "Добро пожаловать в контрольную панель для установки Hydropump 2.1, которая позволяет вам контролировать и конфигурировать состояние активной установки гидронасоса. Если у вас есть какие-либо вопросы, обратитесь в службу поддержки по телефону +1 294 234 098";
$signinbtn = "войти в систему";
$adminwelcome = "Добро пожаловать в админ-панель. Пожалуйста, выберите ваш выбор из вариантов ниже";
$adminvalve = "Вы можете управлять всеми клапанами ниже. Обратите внимание, что клапан не может быть отключен, если давление слишком велико.";
$adminsafety = "Всегда помните о соблюдении техники безопасности!";
$inspect = "Проверить";
$inspectlong = "Убедитесь, что все ваши загруженные параметры правильны, запустив их в соответствующей среде тестирования. Проблемы с конфигурацией - это проблема номер один с развертываниями и не покрываются вашим соглашением об обслуживании.";
$submit = "Отправить";
$submitlong = "Отправьте свои настройки на бэкэнд. Обратите внимание, что до новых настроек может пройти до трех циклов. Если никаких изменений не произошло, обратитесь в службу поддержки.";
$validate = "Проверить";
$validatelong = "После отправки убедитесь, что ваши настройки имеют желаемый эффект. Если что-то не так, вернитесь к предыдущей конфигурации как можно скорее.";

$historytitle = "Ниже перечислены все файлы, которые были загружены в прошлом. Обратите внимание, что они очищаются каждые 10 минут, чтобы система не становилась чистой и чистой.";

$enterconfig = "Пожалуйста, введите новые конфигурационные файлы.";

$btnupdate = "Обновить конфигурацию";
$btnview = "Просмотр истории конфигурации";
$btncontrol = "Управление клапаном";
$statetitle = "Текущий рабочий статус. Для внесения изменений необходимо пройти аутентификацию.";


$flag = "CSCBE{LFI_through_BASE64_FTW}";

?>


```