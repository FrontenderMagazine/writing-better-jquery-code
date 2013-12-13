*By [Mathew Carella][1]*

There are a lot of articles that discuss jQuery and JavaScript performance.
However, in this article I plan to summarize a bunch of speed tips and some of 
my own advice to improve your jQuery and JavaScript code. Better code means 
faster apps and jank free websites. Fast rendering and reactivity means a better
user experience.

First of all, it is important to keep in mind that jQuery *is* JavaScript. This
means we should adopt the same coding conventions,[style guides][2] and 
[best practices][3] for both of them.

First of all, if you are new to JavaScript, I recommend that you to read this
article on[JavaScript best practices for beginners][4] and this one on 
[writing high quality JavaScript][5] before starting to mess with jQuery.

When you are ready to use jQuery, I strongly recommend that you follow those
guidelines:

## Var Caching

DOM traversal can be expensive, so try to cache selected elements when they
will be reused.

    // bad
    
    h = $('#element').height();
    $('#element').css('height',h-20);
    
    // good
    
    $element = $('#element');
    h = $element.height();
    $element.css('height',h-20);

## Avoid Globals

With jQuery, as with JavaScript in general, it is best to ensure that your
variables are properly scoped within your functions.

    // bad
    
    $element = $('#element');
    h = $element.height();
    $element.css('height',h-20);
    
    // good
    
    var $element = $('#element');
    var h = $element.height();
    $element.css('height',h-20);

## Use Hungarian Notation

Putting the dollar symbol at the front of variables makes it easy to recognize
that this item contains a jQuery object.

    // bad
    
    var first = $('#first');
    var second = $('#second');
    var value = $first.val();
    
    // better - we use to put $ symbol before jQuery-manipulated objects
    
    var $first = $('#first');
    var $second = $('#second'),
    var value = $first.val();

## Use Var Chaining (Single Var Pattern)

Rather than having multiple var statements, you can combine them into a single
statement. I suggest placing any variables without assigned values at the end.

    var 
      $first = $('#first'),
      $second = $('#second'),
      value = $first.val(),
      k = 3,
      cookiestring = 'SOMECOOKIESPLEASE',
      i,
      j,
      myArray = {};

## Prefer ‘On’

Recent versions of jQuery have changed whereby functions like `click()` are
shorthand for`on('click')`. In prior versions the implementation was different
whereby`click()` what shorthand for `bind()`. As of jQuery 1.7, `on()` is the
preferred method for attaching event handlers. However, for consistency you can 
simply use`on()` across the board.

    // bad
    
    $first.click(function(){
    	$first.css('border','1px solid red');
    	$first.css('color','blue');
    });
    
    $first.hover(function(){
    	$first.css('border','1px solid red');
    })
    
    // better
    $first.on('click',function(){
    	$first.css('border','1px solid red');
    	$first.css('color','blue');
    })
    
    $first.on('hover',function(){
    	$first.css('border','1px solid red');
    })

## Condense JavaScript

In general, it is preferable to try to combine functions wherever possible.

    // bad
    
    $first.click(function(){
    	$first.css('border','1px solid red');
    	$first.css('color','blue');
    });
    
    // better
    
    $first.on('click',function(){
    	$first.css({
    		'border':'1px solid red',
    		'color':'blue'
    	});
    });

## Use Chaining

Following on the above rule, jQuery makes it easy to chain mathods together.
Take advantage of this.

    // bad
    
    $second.html(value);
    $second.on('click',function(){
    	alert('hello everybody');
    });
    $second.fadeIn('slow');
    $second.animate({height:'120px'},500);
    
    // better
    
    $second.html(value);
    $second.on('click',function(){
    	alert('hello everybody');
    }).fadeIn('slow').animate({height:'120px'},500);

## Keep Your Code Readable

When trying to condense your scripts and utilize chaining, code can sometimes
become unreadable. Try to utlize tabs and new lines to keep things looking 
pretty.

    // bad
    
    $second.html(value);
    $second.on('click',function(){
    	alert('hello everybody');
    }).fadeIn('slow').animate({height:'120px'},500);
    
    // better
    
    $second.html(value);
    $second
    	.on('click',function(){ alert('hello everybody');})
    	.fadeIn('slow')
    	.animate({height:'120px'},500);

## Prefer Short-circuiting

[Short-curcuit evaluation][6] are expressions evaluated from left-to-right and
use the`&&` (logical and) or `||` (logical or) operators.

    // bad
    
    function initVar($myVar) {
    	if(!$myVar) {
    		$myVar = $('#selector');
    	}
    }
    
    // better
    
    function initVar($myVar) {
    	$myVar = $myVar || $('#selector');
    }

## Prefer Shortcuts

One of the ways to condense your code is to take advantage of coding shortcuts
.

    // bad
    
    if(collection.length > 0){..}
    
    // better
    
    if(collection.length){..}

## Detach Elements When Doing Heavy Manipulations

If you are going to do heavy manupipulation of a DOM element, it is recommended
that you first detach it and then re-append it.

    // bad
    
    var 
    	$container = $("#container"),
    	$containerLi = $("#container li"),
    	$element = null;
    
    $element = $containerLi.first(); 
    //... a lot of complicated things
    
    // better
    
    var 
    	$container = $("#container"),
    	$containerLi = $container.find("li"),
    	$element = null;
    
    $element = $containerLi.first().detach(); 
    //...a lot of complicated things
    
    $container.append($element);

## Know the Tricks

When using methods within jQuery that you may have less experience with, be
sure to check the[documentation][7] as there may be a preferable or faster way
to use it.

    // bad
    
    $('#id').data(key,value);
    
    // better (faster)
    
    $.data('#id',key,value);

## Use Subqueries Caching Parents

As mentioned earlier, DOM traversal is an expensive operation. It is typically
better to cache parent elements and reuse these cached elements when selecting 
child elements.

    // bad
    
    var 
    	$container = $('#container'),
    	$containerLi = $('#container li'),
    	$containerLiSpan = $('#container li span');
    
    // better (faster)
    
    var 
    	$container = $('#container '),
    	$containerLi = $container.find('li'),
    	$containerLiSpan= $containerLi.find('span');

## Avoid Universal Selectors

When combined with other selectors, the universal selector is extremely slow.

    // bad
    
    $('.container > *'); 
    
    // better
    
    $('.container').children();

## Avoid Implied Universal Selectors

When you leave off the selector, the universal selector (`*`) is still implied
.

    // bad
    
    $('.someclass :radio'); 
    
    // better
    
    $('.someclass input:radio');

## Optimize Selectors

For example, using an ID should already be sufficiently specific, so there is
no need to add additional selector specificity.

    // bad
    
    $('div#myid'); 
    $('div#footer a.myLink');
    
    // better
    $('#myid');
    $('#footer .myLink');

## Don’t Descend Multiple IDs

Again, when used properly, ID’s should be sufficiently specific not to
require the additional specificity of multiple descendant selectors.

    // bad
    
    $('#outer #inner'); 
    
    // better
    
    $('#inner');

## Try to Use the Latest Version

The newest version is usually the best one: sometimes lighter and sometimes
faster. Obviously, you need to account for compatibility with the code you are 
supporing. For example, don’t forget that from version 2.0 there’s no more 
support for IE 6/7/8.

## Don’t Use Deprecated Methods

It is important to always keep an eye on [deprecated methods][8] for each new
version and try avoid using them.

    // bad - live is deprecated
    
    $('#stuff').live('click', function() {
      console.log('hooray');
    });
    
    // better
    $('#stuff').on('click', function() {
      console.log('hooray');
    });

## Load jQuery Code from a CDN

The Google CDN quickly delivers the script from the user’s nearest cache
location. To use the Google CDN, use the following url for this
<http://code.jQuery.com/jQuery-latest.min.js>

## Combine jQuery with Native JavaScript When Needed

As I was saying before, jQuery is JavaScript, and this means that in jQuery we
can do the same things we do with native JavaScript. Writing in native (or
[vanilla][9]) JavaScript can somtimes mean less readable and less maintainable
code and longer files. But it also can mean faster code. Keep in mind that no 
one framework can be smaller, lighter and faster than native JavaScript 
operations. (Note: click the image to run the test
)

![jq][10]

Due to this performance gap between vanilla JavaScript and jQuery, I strongly
recomend mixing both of them in a wise way, using (when you can) the
[native equivalent of jQuery functions][11].

## Final Considerations

Finally, I recommend this article on [increasing jQuery performance][12] that
includes a number of other good practices that you will find interesting if you 
want a deeper look about the topic.

Keep in mind that using jQuery isn’t a rquirement, but a choice. Consider why
you are using it. DOM manipulations? Ajax? Templating? CSS animations? A 
selector engine? Sometimes, it may be worth considering a
[micro JavaScript framework][13] or a [ jQuery’s custom build][14]that is
specifically tailored on your needs.

*This article was originally published at 
<http://blog.mathewdesign.com/2013/11/14/writing-performant-and-quality-jquery-code/>
*

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