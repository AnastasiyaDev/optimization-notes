# Заметки по книге [Web Performance in Action](https://www.dropbox.com/s/rjt175gbuhc705x/Wagner%20J.%20-%20Web%20Performance%20in%20Action.%20Building%20Faster%20Web%20Pages%20-%202016.PDF?dl=0) и не только

## Глава 1 (Understanding web performance)

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

<table cellpadding="0" cellspacing="0" width="100%">
	<tr>
		<td width="280px" align="center">
			<img src="./images/optimazeImage/Kerastase_ORIG.png" alt="ORIG" width="100%">
			<span>оригинал - 127КБ</span>
		</td>
		<td width="280px" align="center">
			<img src="./images/optimazeImage/Kerastase_ImageOptim.png" alt="ImageOptim" width="100%">
			<span>ImageOptim - 120КБ</span>
		</td>
		<td width="280px" align="center">
			<img src="./images/optimazeImage/Kerastase_TINYPNG.png" alt="TINYPNG" width="100%">
			<span>TINYPNG - 30КБ</span>
		</td>
	</tr>
</table>

### 1.6. Summary

* Анализ веса страницы с помощью `Developer Tools` в Google Chrome
* Минификация текстовых ресурсов, которая удаляет ненужные пробелы из файлов, не затрагивая их функции
* Дальнейшее сокращение размера этих текстовых ресурсов с помощью сжатия на стороне сервера
* Оптимизация изображений

Наибольший прирост к скорости загрузки был после оптимизации изображений - **30%** на тестовой странице, против **10%** у сжатия css, js и **14%** после использования сжатия на сервере

## Глава 2 (Using assessment tools)

[Google PageSpeed Insight](https://developers.google.com/speed/pagespeed/insights/)  

inline-CSS (вставленый через `<style>`) позволяет загружать стили одновременно с HTML, сокращает HTTP-запросы, что хорошо для серверов с `HTTP/1`, увеличивает скорость рендеринга документа, но не кэшируется  

`Google Analytic`  

### 2.3.1. Viewing timing information  

Можно почитать об это в [документации от гугл](https://developers.google.com/web/tools/chrome-devtools/network-performance/reference#timing-explanation)  


`Time to First Byte(TTFB)` - количество времени между моментом, когда пользователь запрашивает веб-страницу и 
моментом поступления первого байта ответа  

`Developer Tools` - чтобы устранинить задержку браузер создает `DNS lookup cache`  

### 2.3.2. Viewing HTTP request and response headers

`Content-Encoding` - в этом заголовке указывается, сжат ли ресурс веб-сервером.  

### 2.4.1. Understanding how browsers render web pages 

![](/images/page-rendering.png)

Этапы рендеринга страницы:  
1. `HTML` загружается с сервера и парсится браузером для построения `DOM`.  
2. После того, как `DOM` построен браузер распарсивает `CSS` и создает `CSSOM`. Это похоже на `DOM`, за исключением того, что он описывает способ применения CSS-правил к документу.  
3. `DOM` и `CSSOM` объединяются для создания дерева рендеринга. Затем дерево рендеринга проходит процесс расположения/планировки (`layout process`), где применяются CSS-правила и элементы размещаются на странице.  
4. Применение стилей, отрисовка в пикселях (растрирование) и отображение.  

Изменения на страницы приводят к повторному рендерингу.  

### 2.4.2. Using Google Chrome’s Timeline tool (Performance)

Инструмент фиксирует четыре конкретных типа событий, каждый из которых имеет цветную кодировку:

* Loading (Blue)—Network-related events such as HTTP requests. It also includes activity such as the parsing of HTML, CSS, and image decoding.  
* Scripting (Yellow)—JavaScript-related events. These can range from DOM-specific activity, to garbage collection, to site-specific JavaScript, and to other activity.  
* Rendering (Purple)—Any and all events relating to page rendering. Events in this category are activities such as applying CSS to the page HTML, and events that cause re-rendering such as changes to the page’s HTML triggered by JavaScript.  
* Painting (Green)—Events related to drawing the layout to the screen, such as layer compositing and rasterization.  
* Other activity - состоит из активности процессора, которую Chrome не может сломать(?) и представить на диаграмме.  

Продолжила изучать [здесь](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/), в книге не совсем актуальные данные, сложнее сопоставлять

1. Первая диаграма **FPS** - чем выше зеленые полосы, тем вышеше *FPS*, красная линия - просидания.  
2. Цвета в диаграмме **CPU** соответствуют цветам на вкладке `Summary`. Если диаграмма **CPU** полона цветом, значит, что CPU был перегружен во время записи.  
3. Раздел **Frames** - выбор зеленого квадрата, показывает количество **FPS**.  
4. Раздел **Main** -  Каждый бар представляет собой событие. Более широкий бар означает, что событие заняло больше времени. Ось-y представляет стек вызовов. Когда вы видите события, укладываются друг на друга. При выборе события на шкале времени, если у него есть красный треугольник в верхнем левом углу, то это плохо. Клик на событие -> внизу в `Summary` -> `reveal` -> 
событие, инициировавшее текущие событие -> `Details` (ссылка на строку кода)


# Best Practices 
### 1. Использовать `rel="noopener"`
Если у ссылки есть `target="_blank"` то нужно прописать ей `rel="noopener"`, для этого есть пара причин, первое - безопасность, если это ссылка на внешний ресурс, то внешний ресурс будет иметь возможность доступа к `window.opener`, второе - 
производительность, окна, запущенные через `target="_blank"` работают в одном процессе и потоке `rel="noopener"` предотвращает работу `window.opener` и кроссдоменный доступ. Основанные на Chromium браузеры создают для новых страниц собственные процессы.  
### 2. Флексы - наше всё (пока не пришли гриды)
Использовать `flexbox` где возможно вместо `float`, тесты производительности [показывают](https://developers.google.com/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing#flexbox) лучший результат в пользу первого.  

### 3. BEM
Снижать сложность селекторов - использовать BEM.

### 4. 60fps = just 16ms

### 5. Изменение стилей через JS
Если надо поменять стиль у элемента в JS (через свойство или навесить класс), то надо делать это у конкретного элемента, а не у родителя, даже когда у вас есть несколько элементов для изменения под одним родителем

### 6. Layout Thrashing (cначала чтение, потом записи в ДОМ)

Код ниже плох тем, что в цикле происходит чтение и сразу же запись стилей (что вызывает перерисовку макета) и чтение уже становиться недействительными, поскольку часть дерева рендеринга изменилась.
```
for (var p = 0; p < paragraphs.length; p++) { 
    var para = paragraphs[p];
    var width = div.offsetWidth;
    para.style.left = width + 'px';
}
```

Меняем код: 
```
var width = div.offsetWidth; 
for (var p = 0; p < paragraphs.length; p++) { 
    var para = paragraphs[p]; 
    para.style.left = width + 'px'; 
}
```
Now it’s read-write-write-write. 

### 7. Границы слоя
У блоков в дереве DOM можно выделить слои, за которые перересовка дерева не выйдет (в табе **Rendering** можно включить показ границ слоев), чтобы создать у элемента искуственные границы можено воспользоваться свойствами `translateZ(0) or backface-visibility: hidden` . Подробнее [раздел 4. Paint Storms](https://calendar.perfplanet.com/2013/the-runtime-performance-checklist/)


# Ресурсы 
* [документатция от Гугла по Chrome DevTools](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)


# Инструменты 
### 1. Rendering tab
Включить - открыть панель разработчика, далее `Cmd+Shift+P`, вводим `Show Rendering` - появится таба **Rendering**, на ней:  
* **Paint Flashing** - всякий раз, когда часть страницы перерисовывается, DevTools выделяет этот раздел зеленым цветом.

### 2. Плагин [Lighthouse](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk)
таба **Audits** доступна в Chrome DevTools

### 3. [Speed Scorecard](https://www.thinkwithgoogle.com/feature/mobile/)
Новый инструмент от Google, который позволяет сравнивать скорость загрузки мобильного сайта с другими интернет-ресурсами. Speed Scorecard отображает скорость загрузки тысяч мобильных сайтов из 12 стран по всему миру (России пока нет).  
