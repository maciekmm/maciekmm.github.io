---
layout: post
title:  "Context menu positioning using pure CSS"
date:   2020-08-01 10:00:00 +0100
tags:
  - css
  - web
  - contextmenu
categories:
  - web
comments: true
---

# Introduction

I was recently challenged with a pretty common, and frankly a simple problem of positioning a custom context menu. When you DuckDuckGo the [*how to position a context menu*](https://duckduckgo.com/?q=positioning+a+context+menu&t=ffab&ia=web) phrase you will be greeted with solutions that either:
- [don't consider boundry conditions](https://stackoverflow.com/a/15795450) or
- [use javascript to check the conditions and position the menu accordingly](https://stackoverflow.com/a/31354591). 

The issue with the first solution is that it's not great in terms of **User Experience**.
The second one is okay, but requires measurements and some javascript logic, it is also not responsive to window resizes.

# A different approach

After some time spent tinkering I came up with a CSS solution to **calculate the context menu position** which respects the window boundries.

## TL;DR


{% capture preview_style %}#menu {
  --mouse-x: 0;
  --mouse-y: 0;
  display: none;
  position: fixed;
  margin: 0;
  left: 0;
  top: 0;
  /* The following line is responsible for all the magic */
  transform: translateX(min(var(--mouse-x), calc(100vw - 100%)))
             translateY(min(var(--mouse-y), calc(100vh - 100%)));
}{% endcapture %}

{% capture preview_appearance %}
	#menu {
		border-radius: 3px;
		width: 150px;
		border: 1px solid #eee;
		list-style: none;
		padding: 0;
		font-size: 10pt;
		color: #777;
    z-index: 5;
    background: #fff;
	}

	#menu li {
		width: 100%;
		height: 30px;
		line-height: 30px;
		padding-left: 20px;
		margin: 4px 0;
	}

	#menu li:hover {
		background: rgba(0,0,0,0.05);
		cursor: pointer;
	}
{% endcapture %}

{% capture full_style %}
  {{ preview_appearance }}
  {{ preview_style }}
{% endcapture %}

```css
{{ preview_style }}
```
{% capture preview_javascript %}const menu = document.querySelector('#menu');
// hide the menu
window.addEventListener('click', event => menu.style.display = 'none')
// show the menu
window.addEventListener('contextmenu', event => {
  event.preventDefault()
  menu.style.setProperty('--mouse-x', event.clientX + 'px')
  menu.style.setProperty('--mouse-y', event.clientY + 'px')
  menu.style.display = 'block'
});{% endcapture %}

{% capture preview_html %}
<ul id='menu'>
	<li>Option 1</li>
	<li>Option 2</li>
</ul>
{% endcapture %}

```javascript
{{ preview_javascript }}
```

## Demo

{% include preview.html style=full_style javascript=preview_javascript html=preview_html message="Right click :)" %}

# Explanation

Let's focus on the *transform* property:

```css
#menu {
  transform: translateX(min(var(--mouse-x), calc(100vw - 100%)))
             translateY(min(var(--mouse-y), calc(100vh - 100%)));
}
```

actually on the _x_ component, as it's analogous to _y_.

```
transform: translateX(min(var(--mouse-x), calc(100vw - 100%)))
```

# Compatibility