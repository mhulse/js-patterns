# JS Patterns

---

Some of my favorite JavaScript plugin design patterns: The Facade Pattern, The Revealing Module Pattern, Immediately-invoked Function Expressions (IIFE)s, The Module Pattern imports and exports

```js
// http://callmenick.com/post/slide-and-push-menus-with-css3-transitions
(function(window) {

	'use strict';

	/**
	 * Extend Object helper function.
	 */
	
	function extend(a, b) {
		var key;
		for (key in b) {
			if (b.hasOwnProperty(key)) {
				a[key] = b[key];
			}
		}
		return a;
	}

	/**
	 * Each helper function.
	 */
	
	function each(collection, callback) {
		var i;
		var item;
		for (i = 0; i < collection.length; i++) {
			item = collection[i];
			callback(item);
		}
	}

	/**
	 * Menu Constructor.
	 */
	
	function Menu(options) {
		this.options = extend({}, this.options);
		extend(this.options, options);
		this._init();
	}

	/**
	 * Menu Options.
	 */
	
	Menu.prototype.options = {
		wrapper: '#o-wrapper', // The content wrapper.
		type: 'slide-left', // The menu type.
		menuOpenerClass: '.c-button', // The menu opener class names (i.e. the buttons).
		maskId: '#c-mask' // The ID of the mask.
	};

	/**
	 * Initialise Menu.
	 */
	
	Menu.prototype._init = function() {
		this.body = document.body;
		this.wrapper = document.querySelector(this.options.wrapper);
		this.mask = document.querySelector(this.options.maskId);
		this.menu = document.querySelector('#c-menu--' + this.options.type);
		this.closeBtn = this.menu.querySelector('.c-menu__close');
		this.menuOpeners = document.querySelectorAll(this.options.menuOpenerClass);
		this._initEvents();
	};

	/**
	 * Initialise Menu Events.
	 */
	
	Menu.prototype._initEvents = function() {
		// Event for clicks on the close button inside the menu.
		this.closeBtn.addEventListener('click', function(e) {
			e.preventDefault();
			this.close();
		}.bind(this));
		// Event for clicks on the mask.
		this.mask.addEventListener('click', function(e) {
			e.preventDefault();
			this.close();
		}.bind(this));
	};

	/**
	 * Open Menu.
	 */
	
	Menu.prototype.open = function() {
		this.body.classList.add('has-active-menu');
		this.wrapper.classList.add('has-' + this.options.type);
		this.menu.classList.add('is-active');
		this.mask.classList.add('is-active');
		this.disableMenuOpeners();
	};

	/**
	 * Close Menu.
	 */
	
	Menu.prototype.close = function() {
		this.body.classList.remove('has-active-menu');
		this.wrapper.classList.remove('has-' + this.options.type);
		this.menu.classList.remove('is-active');
		this.mask.classList.remove('is-active');
		this.enableMenuOpeners();
	};

	/**
	 * Disable Menu Openers.
	 */
	
	Menu.prototype.disableMenuOpeners = function() {
		each(this.menuOpeners, function(item) {
			item.disabled = true;
		});
	};

	/**
	 * Enable Menu Openers.
	 */
	
	Menu.prototype.enableMenuOpeners = function() {
		each(this.menuOpeners, function(item) {
			item.disabled = false;
		});
	};

	/**
	 * Add to global namespace.
	 */
	
	window.Menu = Menu;
	
})(window);

var slideLeft = new Menu({
	wrapper: '#o-wrapper',
	type: 'slide-left',
	menuOpenerClass: '.c-button',
	maskId: '#c-mask'
});

var slideLeftBtn = document.querySelector('#c-button--slide-left');

slideLeftBtn.addEventListener('click', function(e) {
	e.preventDefault();
	slideLeft.open();
});

//----------------------------------------------------------------------------------------------

// https://addyosmani.com/resources/essentialjsdesignpatterns/book/
// http://www.phpied.com/3-ways-to-define-a-javascript-class/
// https://gist.github.com/mhulse/2abd5ffd828e0fae45cbeec86317b97d
// https://gist.github.com/mhulse/3068831
// http://stackoverflow.com/a/1535687/922323

// jQuery-centric (based on above example):
(function(namespace, $, undefined) {
	
	'use strict';
	
	var self;
	
	// Constructor:
	function Menu(options) {
		
		// Private variable:
		var privateVariable = 'foo';
		
		console.log('instantiated');
		
		// Public variable:
		this.publicVariable = 'bar';
		
		this.options = $.extend({}, this.defaults, options);
		
		self = this;
		
		this._init();
		
	}
	
	// Object default options:
	Menu.prototype.defaults = {
		billy: 'bobby'
	};
	
	// Static variable shared by all instances:
	Menu.staticProperty = 'baz';
	
	// Public property:
	Menu.prototype.test = 'foo';
	
	// Instance method will be available to all instances but only load once in memory:
	Menu.prototype.publicMethod = function() {
		
		alert(this.publicVariable);
		
	};
	
	// Private instance method:
	Menu.prototype._init = function() {
		
		console.log('_init', this.options);
		
		this._private.foo();
		
	};
	
	Menu.prototype._private = {};
	
	Menu.prototype._private.foo = function() {
		
		console.log('_private', self.options.billy);
		
	};
	
	// Add to namespace global:
	namespace.Menu = Menu;
	
})((window.FT = (window.FT || {})), jQuery);

var a = new FT.Menu();
a.test = 'baz';
var b = new FT.Menu({ billy: 'bubba' });
console.log(a.test, b.test);

// Output:
//
// instantiated
// _init Object {billy: "bobby"}
// _private bobby
// instantiated
// _init Object {billy: "bubba"}
// _private bubba
// baz foo
```

More:

```js
// https://addyosmani.com/resources/essentialjsdesignpatterns/book/
// http://benalman.com/news/2010/11/immediately-invoked-function-expression/
// https://carldanley.com/js-facade-pattern/

///
/// The Facade Pattern
/// https://carldanley.com/js-facade-pattern/
///

var MyModule = (function(window, undefined) {
    function myMethod() {
        alert('my method');
    }
    function myOtherMethod() {
        alert('my other method');
    }
    // explicitly return public methods when this object is instantiated
    return {
        someMethod: myMethod,
        someOtherMethod: myOtherMethod
    };
})(window);
//  example usage
MyModule.myMethod(); // undefined
MyModule.myOtherMethod(); // undefined
MyModule.someMethod(); // alerts "my method"
MyModule.someOtherMethod(); // alerts "my other method"

///
/// The Facade + Module Pattern Hybrid
/// https://addyosmani.com/resources/essentialjsdesignpatterns/book/#facadepatternjavascript
///

var module = (function() {
    var _private = {
        i: 5,
        get: function() {
            console.log("current value:" + this.i);
        },
        set: function(val) {
            this.i = val;
        },
        run: function() {
            console.log("running");
        },
        jump: function() {
            console.log("jumping");
        }
    };
    return {
        facade: function(args) {
            _private.set(args.val);
            _private.get();
            if (args.run) {
                _private.run();
            }
        }
    };
}());
// Outputs: "current value: 10" and "running"
module.facade({run: true, val: 10});

//------------------------------------------------------------------------------

///
/// The Revealing Module Pattern
/// https://carldanley.com/js-revealing-module-pattern/
///

var MyModule = (function(window, undefined) {
    // revealing module pattern ftw
    function MyModule() {
        function someMethod() {
            alert('some method');
        }
        function someOtherMethod() {
            alert('some other method');
        }
        // expose publicly available methods
        return {
            // in our normal revealing module pattern, we'd do the following:
            someMethod: someMethod,
            // in the facade pattern, we mask the internals so no one has direct access by doing this:
            someMethod: function() {
                someMethod();
            }
        };
    }
})(window);

//------------------------------------------------------------------------------

///
/// Immediately-invoked Function Expressions (IIFE)s
/// https://addyosmani.com/resources/essentialjsdesignpatterns/book/
/// NAMESPACE EXTENSION
///

// namespace (our namespace name) and undefined are passed here
// to ensure 1. namespace can be modified locally and isn't
// overwritten outside of our function context
// 2. the value of undefined is guaranteed as being truly
// undefined. This is to avoid issues with undefined being
// mutable pre-ES5.
;(function (namespace, undefined) {
    // private properties
    var foo = "foo";
    var bar = "bar";
    // public methods and properties
    namespace.foobar = "foobar";
    namespace.say = function (msg) {
        speak(msg);
    };
    namespace.sayHello = function () {
        namespace.say("hello world");
    };
    // private method
    function speak(msg) {
        console.log("You said: " + msg);
    };
    // check to evaluate whether "namespace" exists in the
    // global namespace - if not, assign window.namespace an
    // object literal
})(window.namespace = window.namespace || {});
// we can then test our properties and methods as follows
// public
// Outputs: foobar
console.log(namespace.foobar);
// Outputs: You said: hello world
namespace.sayHello();
// assigning new properties
namespace.foobar2 = "foobar"; 
// Outputs: foobar
console.log(namespace.foobar2);

///
/// Extensibility is of course key to any scalable namespacing pattern
///

// let's extend the namespace with new functionality
(function(namespace, undefined) {
    // public method
    namespace.sayGoodbye = function () {
        namespace.say("goodbye");
    }
})(window.namespace = window.namespace || {});
// Outputs: goodbye
namespace.sayGoodbye();

//------------------------------------------------------------------------------

///
/// The Module Pattern, variations
/// https://addyosmani.com/resources/essentialjsdesignpatterns/book/
/// IMPORTS
///

// Global module
var myModule = (function (jQ, _) {
    function privateMethod1() {
        jQ(".container").html("test");
    }
    function privateMethod2(){
        console.log(_.min([10, 5, 100, 2, 1000]));
    }
    return{
        publicMethod: function(){
            privateMethod1();
        }
    };
})(jQuery, _); // Pull in jQuery and Underscore
myModule.publicMethod();

///
/// The Module Pattern, variations
/// EXPORTS
///

// Global module
var myModule = (function () {
    // Module object
    var module = {};
    var privateVariable = "Hello World";
    function privateMethod() {
        // ...
    }
    module.publicProperty = "Foobar";
    module.publicMethod = function () {
        console.log(privateVariable);
    };
    return module;
})();
```

Mine:

```js
/* global jQuery, namespace */

///
/// Immediately-invoked Function Expressions (IIFE)s
/// Namespace Extension
/// Module Pattern with Imports and Exports
///

(function($public, $window, undefined) {
	
	var _private = {};
	
	_private.i = 5;
	
	_private.get = function() {
		console.log('current value:' + this.i);
	};
	
	_private.set = function(val) {
		this.i = val;
	};
	
	_private.run = function() {
		console.log('running');
	};
	
	_private.speak = function(msg) {
		console.log('You said: ' + msg);
	};
	
	_private.jump = function() {
		console.log('jumping');
	};
	
	$public.say = function(msg) {
		_private.speak(msg);
	};
	
	$public.init = function(args) {
		
		_private.set(args.val);
		
		_private.get();
		
		if (args.run) {
			_private.run();
		}
		
		console.log(typeof $window);
			
	};
	
}(window.namespace = (window.namespace || {}), window, jQuery));

window.addEventListener('DOMContentLoaded', function() {
	
	namespace.init({
		run: true,
		val: 10
	});
	
	// Let’s extend the namespace with new functionality:
	(function($public, undefined) {
		
		$public.sayGoodbye = function() {
			this.say('goodbye');
		};
		
	})(window.namespace = (window.namespace || {}));
	
	namespace.sayGoodbye(); // goodbye
	
});
```

---

Just an example JS plugin pattern I like to use as starting point for utility-esque JS projects/needs.

```js
/**
 * Plugin example using pure javascript.
 *
 * Patterns used: "closure", "alias" and "namespace extension".
 *
 * @see http://stackoverflow.com/a/12774919/922323
 * @param {object} stub
 * @param {object} window
 * @param {object} document
 * @param {undefined} undefined
 * @return void
 */

(function(stub, window, document, undefined) {
	
	'use strict';
	
	stub.init = function(argument) {
		
		// Call public function:
		this.public_func(argument);
		
		// Call private function:
		_private_func_1(argument);
		
		// Call private function with a given `this` value:
		_private_func_2.call(this, argument);
		
		var $wallpaper = document.getElementById(argument);
		
		console.log($wallpaper);
		
		var json = $wallpaper.getAttribute("data-wallpaper-options");
		
		var obj = window.JSON.parse(json);
		
		console.log(obj.image1);
		
	};
	
	stub.public_func = function(argument) {
		
		console.log('public_func', this, argument);
		
	};
	
	var _private_func_1 = function(argument) {
		
		console.log('_private_func_1', this, argument);
		
	},
	
	_private_func_2 = function(argument) {
		
		console.log('_private_func_2', this, argument);
		
	};
	
}((window.STUB = window.STUB || {}), window, document, undefined));

STUB.init('wallpaper');
```

```html
<a id="wallpaper" href="http://google.com" data-wallpaper-options='{"image1":"1230x800.jpg","image2":"1560x800.jpg"}'></a>
<script src="plug-stub.js"></script>
<script>
	/*
	window.onload = function() {
		var console = (window.console || { log : function() {}, warn : function() {} });
		console.log('foo', 'yo');
		STUB.init('foo');
		STUB.public_func('baz');
	};
	*/
</script>
```

---

Just playing around with a basic jQuery plugin pattern.

```js
;(function($, window, document, undefined) {
	
	'use strict'; // Lint setting.
	
	//--------------------------------------------------------------------------
	//
	// Globals:
	//
	//--------------------------------------------------------------------------
	
	var console = window.console || { log : function() {}, warn : function() {} },
	
	NS = 'huh',
	
	defaults = {
		
		foo: '#foo',
		bar: '#bar',
		baz: '#baz'
		
	},
	
	//--------------------------------------------------------------------------
	//
	// Public methods:
	//
	//--------------------------------------------------------------------------
	
	methods = {
		
		init: function(options) {
			
			return this.each(function() {
				
				var settings = $.extend({}, defaults, options),
				    $this    = $(this),
				    data     = $this.data(NS);
				
				if ( ! data) {
					
					// Moved object lookups here, just in case `$this` already has `data`:
					
					var $foo = $(settings.foo),
					    $bar = $(settings.bar),
					    $baz = $(settings.baz);
					
					$this.data(NS, {
						
						target   : $this,
						settings : settings,
						foo      : $foo,
						bar      : $bar,
						baz      : $baz,
						init     : false
						
					});
					
					data = $this.data(NS); // Make it easy for the rest of `init()`.
					
				}
				
				if ( ! data.init) {
					
					data.init = true;
					
					// Let's keep `init()` clean, and move to a private `_main()`:
					
					//_main.call($this, data);
					_main.call($this);
					
				} else {
					
					console.warn('jQuery.' + NS, 'already initialized on', this);
					
					return this;
					
				}
				
			});
			
		},
		
		//----------------------------------
		
		/**
		* Foo test.
		*/
		
		foo: function(elem) {
			
			//----------------------------------
			// Handle API invoked method:
			//----------------------------------
			
			if (typeof elem == 'undefined') {
				
				elem = this;
				
			}
			
			//----------------------------------
			// Loop & return each `this`:
			//----------------------------------
			
			return elem.each(function() {
				
				//----------------------------------
				// Local variable(s):
				//----------------------------------
				
				var $this = $(this),
				    data  = $this.data(NS);
				
				//----------------------------------
				// Stuff here:
				//----------------------------------
				
				console.log($this, data);
				
				// ...
				
			});
			
		} // foo()
		
	},
	
	//--------------------------------------------------------------------------
	//
	// Private methods:
	//
	//--------------------------------------------------------------------------
	
	/**
	 * About this function here.
	 *
	 * @type { function }
	 * @this { object.jquery }
	 */
	
	_main = function(data) {
		
		if (typeof data == 'undefined') {
			
			data = this.data(NS);
			
		}
		
		// From this point forward, access everthing using `data.xxx` (otherwise, $xxx will be out of scope due to setup above):
		
		if (data.foo.length && data.bar.length && data.baz.length) {
			
			console.log(data.foo, data.bar, data.baz);
			
			// Do something with data.foo, data.bar & data.baz!
			
			methods.foo.call(this); // Example of calling public `foo()` where `this` === $(this).
			
		}
		
	}; // _main()
	
	//--------------------------------------------------------------------------
	//
	// Method calling logic:
	//
	//--------------------------------------------------------------------------
	
	/**
	 * Boilerplate plugin logic.
	 *
	 * @see rgne.ws/OvKpPc
	 *
	 * @type { function }
	 * @param { string } method String method identifier.
	 * @return { method } Calls plugin method with supplied params.
	 *
	 * @constructor
	 */
	
	$.fn[NS] = function(method) {
		
		if (methods[method]) {
			
			return methods[method].apply(this, Array.prototype.slice.call(arguments, 1));
			
		} else if ((typeof method === 'object') || ( ! method)) {
			
			return methods.init.apply(this, arguments);
			
		} else {
			
			$.error('Method ' +  method + ' does not exist on jQuery.' + NS);
			
		}
	
	};
	
}(jQuery, window, document));
```

```html
<style>
	div {
		margin: 20px;
		padding: 10px;
		background: #eee;
		border: 1px solid #ddd;
	}
</style>
	
<div id="init"><p>Init!</p></div>

<div id="foo"><p>Foo!</p></div>

<div id="bar"><p>Bar!</p></div>

<div id="baz"><p>Baz!</p></div>

<hr>

<div id="init2"><p>Init #2!</p></div>

<div id="foo2"><p>Foo #2!</p></div>

<div id="bar2"><p>Bar #2!</p></div>

<div id="baz2"><p>Baz #2!</p></div>

<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
<script src="jquery.huh.js"></script>

<script>
	$(document).ready(function() {
		$('#init').huh();
		$('#init2').huh({
			foo: '#foo2',
			bar: '#bar2',
			baz: '#baz2'
		});
	});
</script>
```

---

Example of a basic jQuery plugin that uses the basic jQuery plugin coding guidelines; uses complex plugin pattern like the one [found here](https://github.com/patrickarlt/jQuery-Plugin-Boilerplate/blob/master/jquery-complexPlugin.js).


```js
/*
 * ...
 */

;(function($) {
	
	var MM = {};
	
	MM.defaults = {
		foo : 'bar',
		hey  : 'ho',
		woot : 'there it is'
	};
	
	$.fn.hooplah = function(method) {
		
		if (methods[method]) {
			
			return methods[method].apply(this, Array.prototype.slice.call(arguments, 1));
			
		} else if (typeof method === 'object' || !method) {
			
			return methods.init.apply(this, arguments);
			
		} else {
			
			$.error('Method ' + method + ' does not exist on jQuery.hooplah.');
			
		}
		
	};
	
	//--------------------------------------------------------------------------
	//
	// Public methods:
	//
	//--------------------------------------------------------------------------
	
	var methods = {
		
		init: function(opts) {
			
			return this.each(function() {
				
				var _   = $(this),
				data    = _.data('hooplah'),
				options = $.extend({}, MM.defaults, opts);
				
				if ( ! data) {
					
					_.data('hooplah', {
						target   : _,
						options  : options,
						c        : ((typeof(console) !== 'undefined') ? true : false)
					});
					data = _.data('hooplah');
					
				}
				
				if ( ! data.initialized) {
					
					data.initialized = true;
					
					// Initialization stuff here...
					
					foo_private_method(data); // Sets up something.
					
					bar_private_method(data); // Sets up something.
					
					baz_private_method(data); // Sets up something.
					
					methods.foo_public_method(data.target.children('div'));
					
				} else {
					
					if (data.c) console.warn('hooplah already initialized on', this);
					
					return this;
				
				}
				
			});
			
		},
		
		foo_public_method: function($elem) {
			
			if (typeof($elem) === 'undefined') var $elem = this;
			
			return $elem.each(function() {
				
				var $$  = $(this),
				data    = $$.parent().data('hooplah'),
				options = data.options;
				
				// Do something with $$ here.
			
			});
			
		},
		
		destroy: function() {
			
			// ...
			
		}
		
	}; // End public methods.
	
	//--------------------------------------------------------------------------
	//
	// Private methods:
	//
	//--------------------------------------------------------------------------
	
	var foo_private_method = function() {
		
		// Private stuff here...
		
	};
	
	var bar_private_method = function() {
		
		// Private stuff here...
		
	}
	
	var baz_private_method = function() {
		
		// Private stuff here...
		
	}
	
})(jQuery);
```

---

2012: My personal jQuery plugin starter template... Based on jQuery's plugin guidelines [found here](http://docs.jquery.com/Plugins/Authoring).

```js
/**
 * jQuery pluginName
 *
 * @author    First Last
 * @link      http://site.com
 * @docs      http://github.com/username/pluginName
 * @copyright Copyright (c) YYYY First Last.
 * @license   Dual licensed under the MIT and GPL licenses.
 * @version   1.0.0
 * @date      YYYY/MM/DD
 */

//----------------------------------

// Notes to self:
//console.profile('profile foo');
// ... code here ...
//console.profileEnd('profile foo');
// ... or:
// console.time('timing foo');
// ... code here ...
// console.timeEnd('timing foo');

//----------------------------------

;(function($) {
	
	'$:nomunge'; // Used by YUI compressor.
	
	//--------------------------------------------------------------------------
	//
	// Constants:
	//
	//--------------------------------------------------------------------------
	
	/**
	 * Commonly used variables.
	 *
	 * @const
	 * @return {boolean} Description goes here.
	 */
	
	var constants = {
		
		NS     : 'pluginName',                                       // Namespace identifier.
		PREFIX : 'pn',                                               // Class prefix.
		C      : ((typeof(console) !== 'undefined') ? true : false), // Check if the javascript console is available.
		
	}, // constants
	
	//--------------------------------------------------------------------------
	//
	// Public methods:
	//
	//--------------------------------------------------------------------------
	
	methods = {
		
		/**
		 * Init constructor.
		 */
		
		init: function(opts) {
			
			//----------------------------------
			// Loop & return each "this":
			//----------------------------------
			
			return this.each(function() {
				
				//----------------------------------
				// Local variable(s):
				//----------------------------------
				
				var _   = $(this),                                                          // Target object.
				data    = _.data(constants.NS),                                             // Namespace instance data.
				options = $.extend({}, settings.external, $.fn[constants.NS].defaults, opts); // Merge settings, defaults and options.
				
				//----------------------------------
				// Initialize data:
				//----------------------------------
				
				if ( ! data) {
					
					//----------------------------------
					// Setup variables (for examle):
					//----------------------------------
					
					//var $divs = _.children('li').children('div');
					
					//----------------------------------
					// Namespaced instance data:
					//----------------------------------
					
					_.data(constants.NS, {
						
						target  : _,
						options : options,
						//divs   : $divs,
						init    : false
						
					});
					data = _.data(constants.NS); // Make it easy for the rest of init().
					
				}
				
				//----------------------------------
				// Data initialization check:
				//----------------------------------
				
				if ( ! data.init) {
					
					//----------------------------------
					// Data initialization flag:
					//----------------------------------
					
					data.init = true;
					
					//----------------------------------
					// Do stuff here:
					//----------------------------------
					
					// .. rest of init ...
					
					methods.foo.call(_); // Example of calling foo() where "this" === $(this).
					
					var foo = bar.call(_, data); // Example of calling bar() where "this" === $(this) and data is passed parameter.
					
					baz.call(_); // Example of calling baz() where "this" === $(this).
					
					$.fn[constants.NS].example.call(_); // Example of calling secondary function where "this" === $(this).
					
					// More stuff here. Don't forget about callbacks:
					
					//----------------------------------
					// Callback:
					//----------------------------------
					
					options.onAfterInit.call(_);
					
				} else {
					
					if (constants.C) console.warn(constants.NS, 'already initialized on', this);
					
					return this;
					
				}
				
			});
			
		}, // init()
		
		//--------------------------------------------------------------------
		
		/**
		 * Foo test.
		 */
		
		foo: function(elem) {
			
			//----------------------------------
			// Handle API invoked method:
			//----------------------------------
			
			if (typeof(elem) === 'undefined') elem = this;
			
			//----------------------------------
			// Loop & return each "this":
			//----------------------------------
			
			return elem.each(function() {
				
				//----------------------------------
				// Local variable(s):
				//----------------------------------
				
				var $$  = $(this),
				data    = $$.data(constants.NS),
				options = data.options;
				
				//----------------------------------
				// Stuff here:
				//----------------------------------
				
				// ...
				
			});
			
		}, // foo()
		
		//--------------------------------------------------------------------
		
		/**
		 * Removes plugin from element.
		 */
		
		destroy: function() {
			
			//----------------------------------
			// Loop & return each "this":
			//----------------------------------
			
			return this.each(function() {
				
				//----------------------------------
				// Local variable(s):
				//----------------------------------
				
				var $$ = $(this);
				
				if ($$.data(constants.NS)) {
					
					// Destroy!
					
				} else {
					
					if (constants.C) console.warn(constants.NS, 'already initialized on', this);
					
				}
			
			});
			
		} // foo()
		
	}, // methods
	
	//--------------------------------------------------------------------------
	//
	// Private methods:
	//
	//--------------------------------------------------------------------------
	
	/**
	 * About this function here.
	 *
	 * @this   <object> ($(document), for example)
	 * @return <void>
	 */
	
	bar = function(data) {
		
		// ...
		
	}, // bar()
	
	//--------------------------------------------------------------------
	
	baz = function() {
		
		// ...
		
	}; // baz()
	
	//--------------------------------------------------------------------------
	//
	// Method calling logic:
	//
	//--------------------------------------------------------------------------
	
	/**
	 * Boilerplate plugin logic.
	 *
	 * @link http://docs.jquery.com/Plugins/Authoring
	 * @constructor
	 */
	
	$.fn[constants.NS] = function(method) {
		
		//----------------------------------
		// Boilerplate:
		//----------------------------------
		
		if (methods[method]) {
			
			return methods[method].apply(this, Array.prototype.slice.call(arguments, 1));
			
		} else if (typeof method === 'object' || !method) {
			
			return methods.init.apply(this, arguments);
			
		} else {
			
			$.error('Method ' + method + ' does not exist on jQuery.' + constants.NS); // Should I override? http://api.jquery.com/jQuery.error/
			
		}
		
	}; // constructor()
	
	//--------------------------------------------------------------------------
	//
	// Public access to secondary functions:
	// http://www.learningjquery.com/2007/10/a-plugin-development-pattern
	// http://stackoverflow.com/questions/11286312/superfish-plugin-fn-extend-how-does-this-code-work
	//
	//--------------------------------------------------------------------------
	
	/**
	 * Just an example.
	 */
	
	$.fn[constants.NS].example = function() {
		
		//----------------------------------
		// Loop & return each "this":
		//----------------------------------
		
		return this.each(function() {
			
			//----------------------------------
			// Local variable(s):
			//----------------------------------
			
			var $$  = $(this),
			data    = $$.data(constants.NS),
			options = data.options;
			
			//----------------------------------
			// Logic here...
			//----------------------------------
			
			// Example of calling a public method:
			methods.foo.call($$); // $.fn[constants.NS]('open', $$);
			
		});
		
	}; // example()
	
	//--------------------------------------------------------------------------
	//
	// Default settings:
	//
	//--------------------------------------------------------------------------
	
	var settings = {}; // Initialize config object.
	
	//----------------------------------
	
	/**
	 * Private settings.
	 */
	
	settings.internal = {
		
		// ...
		
	}; // settings.internal
	
	//----------------------------------
	
	/**
	 * Public settings.
	 */
	
	settings.external = {
		
		// ...
		onAfterInit : function() {} // After plugin initialization.
		// ...
		
	}; // settings.external
	
	//----------------------------------
	
	$.fn[constants.NS].defaults = settings.external; // http://stackoverflow.com/questions/11306375/plugin-authoring-how-to-allow-myplugin-defaults-key-value
	
})(jQuery);
```

Note to self:

```js
/**
 * @see http://stackoverflow.com/a/920322/922323
 */

$.fn.pluginName.exists = function() {

    return ( !! this.length);

};
```

Put inside plugin and call like so:

```js
$('#foo').pluginName.exists()
```

---

jQuery plugin "shell" for utility class. [Discussion and more code here](http://codereview.stackexchange.com/questions/19799/a-jquery-utility-plugin-template-looking-for-peer-pro-feedback).

```js
;(function($, window, document, undefined) {
  
	"use strict";
	
	var console = window.console || { log : function() {}, warn : function() {} },
	
	defaults = {
		
		api     : 'http://advertising.registerguard.com/manager/',
		parent  : '',
		section : '',
		page    : '',
		cb      : (Math.round(Math.random() * 10000000000000000)),
		
	},
	
	methods = {
		
		init : function(options) {
			
			var settings = $.extend({}, defaults, options);
		
	},
	
	success = function(settings, json) {
		
	},
	
	ad_code = function(settings, breakpoint, target, flag) {
		
	};
	
	$.ad_manager = function(method) {
		
		if (methods[method]) {
			
			return methods[method].apply(this, Array.prototype.slice.call(arguments, 1));
			
		} else if ((typeof method === 'object') || ( ! method)) {
			
			return methods.init.apply(this, arguments);
			
		} else {
			
			$.error('Method ' + method + ' does not exist on jQuery.ad_manager.');
			
		}
		
	};
	
}(jQuery, window, document));
```

---

Adobe Illustrator JSX boilerplate. Starter script for new ExtendScript JS plugins.

```
/* jshint laxbreak:true, -W043, -W030 */
/* globals app */

// jshint ignore:start
#target illustrator
#targetengine main
// jshint ignore:end

/**
 * @@@BUILDINFO@@@ Boilerplate.jsx !Version! Fri Dec 25 2015 22:47:45 GMT-0800
 *
 * @see https://gist.github.com/mhulse/5f2fb7dbe48b65cd6861
 */

var NS = 'BP';

this[NS] = (function(_$this, _$application, _$window, undefined) {
	
	'use strict';
	
	//----------------------------------------------------------------------
	// Private variables:
	//----------------------------------------------------------------------
	
	var _private = {};
	
	//----------------------------------------------------------------------
	// Private methods:
	//----------------------------------------------------------------------
	
	/**
	 * Internal init/constructor.
	 *
	 * @return {void}
	 */
	
	_private.main = function() {
		
		
		
	};
	
	//----------------------------------------------------------------------
	// Public methods:
	//----------------------------------------------------------------------
	
	/**
	 * Constructor.
	 *
	 * @param  {string} $title Title of palette window.
	 * @return {void}
	 */
	
	_$this.init = function($title) {
		
		
		
	};
	
	//----------------------------------------------------------------------
	// Return public API:
	//----------------------------------------------------------------------
	
	return _$this;
	
}((this[NS] || {}), app, Window));

//----------------------------------------------------------------------
// Initialize plugin:
//----------------------------------------------------------------------

this[NS].init();
```

---

Copyright © 2017 [Michael Hulse](http://mky.io).

Licensed under the Apache License, Version 2.0 (the “License”); you may not use this work except in compliance with the License. You may obtain a copy of the License in the LICENSE file, or at:

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an “AS IS” BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

<img src="https://github.global.ssl.fastly.net/images/icons/emoji/octocat.png">