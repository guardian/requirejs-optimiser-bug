
- foo.js is a module, reqwest.js is a third party module
- foo.js requires reqwest.js
- bar.js requires foo.js

Install requirejs and run the test,

```
npm -g install requirejs
./test.sh
```

Contents of tmp/bar.js after the compilation,

``` 
!function (name, definition) {    <----- i. the original 'reqwest' module
      if (typeof module != 'undefined') module.exports = definition()
            else if (typeof define == 'function' && define.amd) define(name, definition)
                  else this[name] = definition()
}('reqwest', function () {
      return {} 
});

define("reqwest",[], function(){});   <----- ii. empty named 'reqwest' module

define('modules/foo',['reqwest'], function (r) { 
        console.log(r);
});

define('bar',['modules/foo'], function (f) { 
        console.log('foo', f);
});
```

The require optimiser produces an empty module (see ii. above) alongside the actual reqwest module (see i. above). 

The means :-

 - curl.js and almond (alternate JS loaders) can not load our code as they use the second, empty module.
 - require.js is working by luck (just by picking the first named module it finds).

