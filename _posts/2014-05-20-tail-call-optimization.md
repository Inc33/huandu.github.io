---
layout: post
title: "Tail call optimization"
description: ""
category: "technical"
tags: [c, compiler, optimization, tail call]
---
{% include JB/setup %}

[Tail call](http://en.wikipedia.org/wiki/Tail_call) is a function call at the end of a function. It might be optimized to a simple `jmp` with some modifications on current stack. Tail call optimization can be used to save stack size and improve function call performance. If tail call is used in a recursive function, it can avoid stack overflow.

Here is a sample borrowed from wikipedia page with some modifications.

	function foo(data) {
		return bar(data)
	}

The above function can be translated to following pseudo assembly code.

	foo:
		mov  reg,[sp+data] ; fetch data from stack (sp) parameter into a scratch register
		push reg            ; put data on stack where B expects it
		call bar            ; bar uses data
		pop                 ; remove data from stack
		ret

Then, change the code with tail call optimization.

	foo:
		jmp bar             ; bar can use foo's stack directly

If arguments of `foo()` cannot be used directly by `bar()`, tail call optimization can still be used just with some limited modifications.

	function foo(data1, data2) {
		return bar(data1 + data2)
	}

As `foo()` doesn't need stack when `bar()` is called, simply reuse `foo()` stack space to store `data1 + data2` result to save stack space.

	foo:
		mov reg, [sp+data2] ; fetch data2 from stack (sp) parameter into a scratch register
		add reg, [sp+data1] ; calculate data1 + data2
		mov [sp+data1], reg ; reuse data1 on stack
		jmp bar             ; let bar uses foo's stack directly

Tail call optimization won't work if the return value doesn't come from a function call directly.

	function foo(data) {
		// tail call optimization won't work here.
		return bar(data) + 1
	}

A real scenario using tail call optimization is to optimize factorial function performance.

If we write factorial function like this, it's slow and may cause stack overflow very soon.

	function factorial(n) {
		if (n <= 1) {
			return 1
		}
		
		// factorial() return value cannot be used directly
		// compiler has to use stack space to save return value
		return n * factorial(n - 1)
	}

A much better way to write factorial function.

	function factorial(n, total = 1) {
		if (n <= 1) {
			return total
		}
		
		return factorial(n - 1, total * n)
	}