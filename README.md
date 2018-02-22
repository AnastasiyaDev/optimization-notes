# Заметки по книге Web Performance in Action

## Глава 1

### 1.1.2. How web browsers talk to web servers 

Первое что нужно сделать, чтобы повысить производительность сайта, это снизить задержки (`latency`) при общении с сервером.  

При использовании протокола `HTTP/1` возникает феномен, называемый Блокировка начала строки (`head-of-line blocking`). 
Это происходит потому, что браузер ограничивает количество запросов, которые он будет делать за один раз (обычно шесть).
Такое поведение увеличивает время загрузки страницы.  

Далее в главе немного о `Chrome Dev Tools` (ничего нового).  
Вес страницы для устройств с ретиной больше чем на устройствах со стандартной плотностью отображения.  


### 1.4 Optimizing the client’s website

При улучшении производительности веб-сайта цель проста: уменьшить объем переданных данных (эта простая концепция работает как для `HTTP/1`, так и для `HTTP/2`). Надо начать с минимизации ресурсов сайта, включая CSS, JavaScript и HTML-код, картинки  

### 1.4.1. Minifying assets 

`npm i -g minifier html-minify` - пакеты для минификации ресурсов  
`minify -o ./css/styles.min.css ./css/styles.css` - минификация css  
`minify -o jquery.min.js jquery.js` - минификация js  
`htmlminify -o index.html index.src.html` - минификация html  

### 1.4.2. Using server compression

Сжатие на сервере работает следующим образом: пользователь запрашивает веб-страницу с сервера, запрос пользователя сопровождается `Accept-Encodingheader`, который сообщает серверу форматы сжатия, которые браузер может использовать. Метод сжатия, называемый `gzip` имеет почти универсальную поддержку браузеров, и очень эффективен в уменьшении размера текстовых ресурсов.  

Проделала практику из книги, сразу заметно уменьшение веса `css(16KB -> 3.1KB)` и `js(84.4KB -> 30KB, 2KB -> 1.1KB)` файлов.

### 1.4.3. Optimizing images 

Пробовала ресурс [tinypng.com](http://tinypng.com) - жмет хорошо, потери качетва на jpg практически незаметны, на png-шках области с тенями/полупрозрачные видно как шакалятся, для примера собрала несколько изображений в папку [/optimazeImage](https://github.com/AnastasiyaDev/optimization-notes/tree/master/images/optimazeImage)  

Слева направо: оригинальная картинка, оптимизация с помощью `ImageOptim`, оптимизация с помощью `tinypng.com`  

<div width="100%">
	<div style="display: inline-block;width: 280px;text-align: center;">
		<img src="./images/optimazeImage/Kerastase_ORIG.png" alt="ORIG" width="100%">
		<span>оригинал - 127КБ</span>
	</div>
	<div style="display: inline-block;width: 280px;text-align: center;">
		<img src="./images/optimazeImage/Kerastase_ImageOptim.png" alt="ImageOptim" width="100%">
		<span>оригинал - 120КБ</span>
	</div>
	<div style="display: inline-block;width: 280px;text-align: center;">
		<img src="./images/optimazeImage/Kerastase_TINYPNG.png" alt="TINYPNG" width="100%">
		<span>оригинал - 30КБ</span>
	</div>
</div>

### 1.6. Summary

* Анализ веса страницы с помощью `Developer Tools` в Google Chrome
* Минификация текстовых ресурсов, которая удаляет ненужные пробелы из файлов, не затрагивая их функции
* Дальнейшее сокращение размера этих текстовых ресурсов с помощью сжатия на стороне сервера
* Оптимизация изображений

Наибольший прирост к скорости загрузки был после оптимизации изображений - **30%** на тестовой странице, против **10%** у сжатия css, js и **14%** после использования сжатия на сервере

## Глава 2

