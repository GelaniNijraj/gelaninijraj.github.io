---
layout: post
comments: true
title: Parsing JSON in C using JSMN
tags: c jsmn json parsing
---

I recently came across this small and really useful library, JSMN 
(pronounced jasmine) which is really easy and useful for parsing JSON 
data in C with small but useful API. 

From its [website](http://zserge.com/jsmn.html) :
> [JSMN is] is designed to be robust (it should work fine even with 
> erroneous data), fast (it parses data on the fly and is re-entrant), 
> portable (no superfluous dependencies or non-standard C extensions). 
> And of course, simplicity is a key feature.

So let's get started!

## Installation

As I've already said, JSMN is really small. And it is opensource with 
MIT license. So you can basically just drop in the library inside your 
project and use it wherever you want.

Clone the git repository using 

```
git clone https://github.com/zserge/jsmn.git
```
or mercurial using
```
hg clone http://bitbucket.org/zserge/jsmn jsmn
```

Run `make` to build the library.

That's about it. Wherever you want to use the library include `jsmn.h` 
and compile with the `libjsmn.a` library.

## Usage

Before doing any parsing, a `jsmn_parser` object needs to be created 
and then initialized. Like this :

```c
#include <jsmn.h>

int main(){
	jsmn_parser parser; // creating the object
	// initializing
	// same function can also be used to reset an object
	jsmn_init(&parser);
	return 0;
}
```

Now we can get to actual parsing using the `jsmn_parse` function. 
Instead of assigning separate memory space for each token in the JSON 
what JSMN does is create pointers which point to start and end of each
tokens. These tokens are stored in `jsmntok_t` structure which is 
defined as below : 

```c
typedef struct {
    jsmntype_t type; /* Token type */
    int start;       /* Token start position */
    int end;         /* Token end position */
    int size;        /* Number of child (nested) tokens */
} jsmntok_t;
```

It's pretty straight forward. Now lets use the `jsmn_parse`.

```c
jsmntok_t tokens[256];
int n;
char *json;

json = ... // store your JSON data in a character pointer

n = jsmn_parse(&parser, json, strlen(json), tokens, 256);
// n will now contain number of parsed tokens or a negative
// error value
```

If you don't know the number of tokens beforehand you can pass `NULL` 
instead of tokens and it will return number of tokens without actually 
storing them so you can allocate memory appropriately.

If you're reading input from a stream then you can continuously check 
if return value of `jsmn_parse` is `JSMN_ERROR_NOMEM` and if it is then 
reallocate more memory.

And that's it. You can traverse through the scanned tokens array and do 
whatever you like with it.

Mind that JSMN does not perform unescaping on the tokens to UTF-8 
escaped token will remain as it is.

## Read more

You can find more information and example code here :

* [http://zserge.com/jsmn.html](https://alisdair.mcdiarmid.org/jsmn-example/)
* [https://alisdair.mcdiarmid.org/jsmn-example/](https://alisdair.mcdiarmid.org/jsmn-example/)

{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://nijraj-gelani.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}
