Есть много статей, в которых обсуждается производительность jQuery и JavaScript.
В этой статье я хочу резюмировать набор советов по производительности (и еще
несколько из моего собственного опыта), которые позволит вам улучшить качество
кода, который вы пишете на jQuery и JavaScript. Более качественный код — это
более быстрые приложения и сайты без лишнего тяжелого мусора. Страницы быстрее
рендерятся, лучше реагируют — пользователь доволен.

Во-первых, важно помнить, что jQuery — это *и есть* JavaScript. Это значит, что
нам нужно использовать одни и те же правила кода, [гайдлайны стиля][2] и
 [передовые практики][3] и для языка, и для работы с библиотекой.

Если вы только начинаете работать с JavaScript, советовую перед тем, как
связываться с jQuery, прочитать статью о [передовых JavaScript-практиках для начинающих][4]
и о том, [как писать качественный JavaScript-код][5].

Когда вы будете готовы использовать jQuery, я настоятельно советую вам следовать
этим принципам:

## Кэшируйте переменные

Поиск по дереву DOM иногда может быть дорогой операцией, поэтому старайтесь
кэшировать выбранные элементы, если они будут использоваться повторно.

    // плохо

    h = $('#element').height();
    $('#element').css('height',h-20);

    // хорошо

    $element = $('#element');
    h = $element.height();
    $element.css('height',h-20);

## Не используйте глобальные переменные

Для jQuery, как и JavaScript в общем, лучше всего делать так, чтобы переменные
были замкнуты внутри своих функций.

    // плохо

    $element = $('#element');
    h = $element.height();
    $element.css('height',h-20);

    // хорошо

    var $element = $('#element');
    var h = $element.height();
    $element.css('height',h-20);

## Используйте венгерскую нотацию

Если в начале переменной стоит символ доллара, легко сразу понять, что эта
переменная содержит jQuery-объект.

    // плохо
    var first = $('#first');
    var second = $('#second');
    var value = $first.val();

    // хорошо - перед объектами, которые управляются jQuery, мы ставим символ $
    var $first = $('#first');
    var $second = $('#second'),
    var value = $first.val();

## Объединяйте объявление переменных в блоки

Вместо того, чтобы писать директиву var для каждой переменной, можно объединить
их в один var-блок. Я советую ставить все переменные без значений в конце блока.

    var
      $first = $('#first'),
      $second = $('#second'),
      value = $first.val(),
      k = 3,
      cookiestring = 'SOMECOOKIESPLEASE',
      i,
      j,
      myArray = {};

## Используйте ‘On’

Последние версии jQuery изменили поведение, так что теперь функции типа `click()`
— это сокращение от `on('click')`. В более ранних версиях `click()` был
сокращением от `bind()`. Начиная с jQuery 1.7 предпочтительный метод для
привязки обработчиков событий — `on()`. Для единообразия, в общем, можно просто
использовать `on()` везде.

    // плохо
    $first.click(function(){
    	$first.css('border','1px solid red');
    	$first.css('color','blue');
    });

    $first.hover(function(){
    	$first.css('border','1px solid red');
    })

    // лучше
    $first.on('click',function(){
    	$first.css('border','1px solid red');
    	$first.css('color','blue');
    })

    $first.on('hover',function(){
    	$first.css('border','1px solid red');
    })

## Компактный JavaScript

В общем, где возможно, лучше совмещать функции в одну.

    // плохо
    $first.click(function(){
    	$first.css('border','1px solid red');
    	$first.css('color','blue');
    });

    // лучше
    $first.on('click',function(){
    	$first.css({
    		'border':'1px solid red',
    		'color':'blue'
    	});
    });

## Используйте цепочки

jQuery позволяет вам очень просто связывать методы в цепочки. Пользуйтесь этим!

    // плохо
    $second.html(value);
    $second.on('click',function(){
    	alert('hello everybody');
    });
    $second.fadeIn('slow');
    $second.animate({height:'120px'},500);

    // лучше
    $second.html(value);
    $second.on('click',function(){
    	alert('hello everybody');
    }).fadeIn('slow').animate({height:'120px'},500);

## Оставляйте код читаемым

Но когда вы пишете компактный код и используете цепочки, иногда код может стать
нечитаемым. Используйте отступы и переносы строк, чтобы код выглядел красиво.

    // плохо
    $second.html(value);
    $second.on('click',function(){
    	alert('всем привет');
    }).fadeIn('slow').animate({height:'120px'},500);

    // лучше
    $second.html(value);
    $second
    	.on('click',function(){ alert('всем привет');})
    	.fadeIn('slow')
    	.animate({height:'120px'},500);

## Используйте короткую оценку выражений

[Короткая оценка][6] означает, что выражения оцениваются слева направо с
использованием операторов `&&` (логическое и) и `||` (логическое или).

    // плохо
    function initVar($myVar) {
    	if(!$myVar) {
    		$myVar = $('#selector');
    	}
    }

    // лучше
    function initVar($myVar) {
    	$myVar = $myVar || $('#selector');
    }

## Сокращайте!

Один из способов сделать код более компактным — воспользоваться сокращениями.

    // плохо
    if(collection.length > 0){..}

    // лучше
    if(collection.length){..}

## Отделяйте элементы, когда нужно провести с ними дорогие операции

Если вы собираетесь провести ряд дорогих операций над элементом DOM,
рекомендуется отделить его от документа, а потом добавить снова.

    // плохо
    var
    	$container = $("#container"),
    	$containerLi = $("#container li"),
    	$element = null;

    $element = $containerLi.first();
    //... много сложных манипуляций

    // лучше
    var
    	$container = $("#container"),
    	$containerLi = $container.find("li"),
    	$element = null;

    $element = $containerLi.first().detach();
    //... много сложных манипуляций

    $container.append($element);

## Знайте особенности

Когда вы используете jQuery-методы, с которыми у вас не так много опыта работы,
обязательно проверьте [документацию][7]: может быть, для вашей задачи есть
заранее продуманный или более быстрый вариант использования.

    // плохо
    $('#id').data(key,value);

    // лучше (быстрее)
    $.data('#id',key,value);

## Кэшируйте родительские элементы для подзапросов

Как я говорил выше, поиск по DOM — дорогая операция. Обычно лучше кэшировать
родительские элементы, а потом использовать закэшированные элементы для выбора дочерних.

    // плохо
    var
    	$container = $('#container'),
    	$containerLi = $('#container li'),
    	$containerLiSpan = $('#container li span');

    // лучше (быстрее)
    var
    	$container = $('#container '),
    	$containerLi = $container.find('li'),
    	$containerLiSpan= $containerLi.find('span');

## Не используйте универсальные селекторы

Универсальный селектор вместе с другими селекторами  — это дико медленно.

    // плохо

    $('.container > *');

    // лучше

    $('.container').children();

## Не используйте имплицитные универсальные селекторы

Если вы не указываете никакого селектора, имплицитно подставляется универсальный
селектор (`*`).

    // плохо
    $('.someclass :radio');

    // лучше
    $('.someclass input:radio');

## Оптимизируйте селекторы

Например, если вы используете id — это уже достаточно специфический запрос,
поэтому не нужно добавлять дополнительную специфичность селекторов.

    // плохо
    $('div#myid');
    $('div#footer a.myLink');

    // лучше
    $('#myid');
    $('#footer .myLink');

## Не используйте в запросе путь из нескольких id

Опять-таки, если вы используете id правильно, то одного id должно быть
достаточно, и дополнительной специфичности из нескольких вложенных селекторов
не требуется.

    // плохо
    $('#outer #inner');

    // лучше
    $('#inner');

## Старайтесь все время использовать последнюю версию

Обычно новая версия — самая лучшая; иногда она легче, иногда быстрее. Не
забывайте, конечно, что вам нужно учитывать совместимость с кодом, который вы
поддерживаете. Например, нужно помнить, что jQuery 2.0 не поддерживает Internet
Explorer 6, 7 и 8.

## Не используйте устаревшие методы

Всегда смотрите на список [устаревших методов][8] в каждой новой версии и
старайтесь их не использовать.

    // плохо - метод live является устаревшим
    $('#stuff').live('click', function() {
      console.log('ура!');
    });

    // лучше
    $('#stuff').on('click', function() {
      console.log('ура!');
    });

## Загружайте jQuery с CDN

CDN от Google быстро отдает пользователю скрипт с ближайшего к нему сервера.
Чтобы использовать CDN Google, используйте в коде следующий адрес:
`http://code.jQuery.com/jQuery-latest.min.js`

## Когда нужно, совмещайте jQuery и встроенный в браузер JavaScript

Как я говорил выше, jQuery — это JavaScript, и, значит, на jQuery мы можем делать
те же самые вещи, что и на встроенном в браузер JavaScript. Когда вы пишете на
нативном («[ванильном][9]») JavaScript, иногда это приводит к длинным файлам с
нечитаемым кодом, который сложно поддерживать. Но это значит и то, что ваш код
будет исполняться быстрее. Помните, что нет такого фреймворка, который был бы
меньше, легче и быстрее, чем операции на нативном JavaScript.

_(Кликните на картинку и проверьте)_

![jq][10]

Из-за этой разнице в производительности между встроенным в браузер JavaScript и
jQuery я настоятельно рекомендую использовать их оба (ответственно), и использовать,
когда это возможно, [нативные аналоги функций jQuery][11].

## Наконец

Наконец, я рекомендую прочитать эту статью об [увеличении производительности jQuery][12].
В ней содержится ряд других хороших советов, которые будут для вас интересны,
если вы хотите поглубже разобраться в этой теме.

Не забывайте, что использовать jQuery – это выбор, а не требование. Подумайте,
зачем вы используете jQuery. Для преобразований DOM? AJAX? Шаблонов? CSS-анимаций?
Как движок селекторов? Иногда имеет смысл использовать [микрофреймворки на JavaScript][13]
или [кастомную сборку jQuery][14], которая будет подогнана четко под ваши потребности.

 [1]: http://flippinawesome.org/authors/mathew-carella
 [2]: https://github.com/airbnb/JavaScript
 [3]: https://github.com/stevekwan/best-practices/blob/master/JavaScript/best-practices.md
 [4]: http://net.tutsplus.com/tutorials/JavaScript-ajax/24-JavaScript-best-practices-for-beginners/
 [5]: http://net.tutsplus.com/tutorials/JavaScript-ajax/the-essentials-of-writing-high-quality-JavaScript/
 [6]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-Circuit_Evaluation
 [7]: http://api.jquery.com/
 [8]: http://api.jQuery.com/category/deprecated/
 [9]: http://vanilla-js.com/
 [10]: img/jq.png
 [11]: http://www.leebrimelow.com/native-methods-jQuery/
 [12]: http://net.tutsplus.com/tutorials/JavaScript-ajax/10-ways-to-instantly-increase-your-jQuery-performance/
 [13]: http://microjs.com/
 [14]: http://net.tutsplus.com/tutorials/JavaScript-ajax/how-to-build-your-own-custom-jQuery/