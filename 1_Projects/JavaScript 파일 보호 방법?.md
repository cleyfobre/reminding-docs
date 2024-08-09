#javascript #web

### Question

I know it's impossible to hide source code but, for example, if I have to link a JavaScript file from my CDN to a web page and I don't want the people to know the location and/or content of this script, is this possible?

For example, to link a script from a website, we use:

```javascript
<script type="text/javascript" src="http://example.com/hello.js">
</script>
```

Now, is possible to hide from the user where the script comes from, or hide the script content and still use it on a web page?

For example, by saving it in my private CDN that needs password to access files, would that work? If not, what would work to get what I want?

### Answer

### Good question with a simple answer: **you can't**!

JavaScript is a client-side programming language, therefore it works on the client's machine, so you can't actually hide anything from the client. Obfuscating your code is a good solution, but it's not enough, because, although it is hard, someone could decipher your code and "steal" your script. There are a few ways of making your code hard to be stolen, but as I said nothing is bullet-proof.

Off the top of my head, one idea is to restrict access to your external js files from outside the page you embed your code in. In that case, if you have

```javascript
<script type="text/javascript" src="myJs.js"></script>
```

and someone tries to access the `myJs.js` file in browser, he shouldn't be granted any access to the script source. For example, if your page is written in PHP, you can include the script via the `include` function and let the script decide if it's _safe_" to return it's source. In this example, you'll need the external "js" (written in PHP) file `myJs.php`:

```javascript
<?php
    $URL = $_SERVER['SERVER_NAME'].$_SERVER['REQUEST_URI'];
    if ($URL != "my-domain.example/my-page.php")
    die("/\*sry, no acces rights\*/");
?>
// your obfuscated script goes here
```

that would be included in your main page `my-page.php`:

```javascript
<script type="text/javascript">
    <?php include "myJs.php"; ?>;
</script>
```

This way, only the browser could see the js file contents.

Another interesting idea is that at the end of your script, you delete the contents of your dom script element, so that after the browser evaluates your code, the code disappears:

```javascript
<script id="erasable" type="text/javascript">
    //your code goes here
    document.getElementById('erasable').innerHTML = "";
</script>
```

These are all just simple hacks that cannot, and I can't stress this enough: cannot, fully protect your js code, but they can sure piss off someone who is trying to "steal" your code.

### Update:

I recently came across [a very interesting article](http://www.patrick-wied.at/blog/a-technique-for-hiding-your-javascript-code) written by [Patrick Weid](http://www.patrick-wied.at/) on how to hide your js code, and he reveals a different approach: you can encode your source code into an image! Sure, that's not bullet proof either, but it's another _fence_ that you could build around your code. The idea behind this approach is that most browsers can use the canvas element to do pixel manipulation on images. And since the canvas pixel is represented by 4 values (rgba), each pixel can have a value in the range of 0-255. That means that you can store a character (actual it's ascii code) in every pixel. The rest of the encoding/decoding is trivial.