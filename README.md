# comicfury-buttons
A tutorial on adding extra navigation buttons to a ComicFury layout.

[examplelayout.html](https://github.com/ksadov/comicfury-buttons/blob/master/examplelayout.html) contains ComicFury's default "Rainbow tables" comic page layout customized with additional nativation buttons. You can see it in action [here.](http://testinglayout.cfw.me/comics/). Adding new navigation buttons requires adding two components: the html tags corresponding to each button, and the JavaScript tag that controls their behavior. 

## html
The button are hyperlinks with a blank `href` (the actual link destination is constructed with JavaScript), an `id` field, an image address (which you can replace with your own image uploaded via ComicFury's "Upload Extra Files" utility) and an `alt` text description. The example code adds two kinds of navigation buttons: those that jump 3 pages, and those that jump 5 pages. I inserted the html for the 3 page navs in the top and bottom navigation bars, and the 5 page navs just along the bottom bar. You can change where the buttons are inserted in the layout, `id`, image address and `alt` to fit your layout and page jump size, but make sure that the new `id` tags are consistent with the JavaScript code below that uses the `id` to retrieve the elements.

Line 15, 3 page back button inserted between the default "first" button and the default "previous" button:
```
<a href="" id="backMedTop"><img src="http://testinglayout.cfw.me/files/prev3.png" alt="back 3 pages" /></a>
```

Line 37, 3 page next button inserted between the default "next" button and the default "most recent comic" button:

```
<a href="" id="nextMedTop"><img src="http://testinglayout.cfw.me/files/next3.png" alt="next 3 pages" /></a>
```
Lines 59 - 61, 3 page back button and 5 page back button inserted between the default "first" button and the default "previous" button:

```
<a href="" id="backMedBottom"><img src="http://testinglayout.cfw.me/files/prev3.png" alt="back 3 pages" /></a> 

<a href="" id="backBig"><img src="http://testinglayout.cfw.me/files/prev5.png" alt="back 5 pages" /></a>
```

Lines 79 - 81, 3 page next button and 5 page next button inserted between the default "next" button and the default "most recent comic" button:

```
<a href="" id="nextMedBottom"><img src="http://testinglayout.cfw.me/files/next3.png" alt="next 3 pages" /></a>
	    
<a href="" id="nextBig"><img src="http://testinglayout.cfw.me/files/next5.png" alt="next 5 pages" /></a>
```

## JavaScript

The script that controls the new button behavior is inserted below the html tags, on lines 85 - 134. When adding your own JavaScript, don't forget to enclose it in `<script>` tags.

First, we assign the value of our jumps to variables:
```
	  const medAmount = 3;
	  const bigAmount = 5;
```

Then we construct variables representing the current page number (according to the ComicFury url) and the latest page number. [ComicFury's layout system](https://comicfury.com/forum/viewthread.php?id=12232) converts the code in square brackets to JavaScript values, which we then parse into numbers using the parseInt() function.

```
	  const currentPg = parseInt([f:js|v:comicnumber]);
	  const latestPg = parseInt([f:js|v:comicsnum]);
```

What happens if we try to jump forward 5 pages on the second-to-last page, or try to jump back 3 pages on page 2? The former case should send you to the latest page, and the latter should send you to the first page. These variables represent the page numbers that hitting the new navigation buttons will send you to:
```
	  const medNextPg = Math.min(currentPg + medAmount, latestPg);
	  const medBackPg = Math.max(currentPg - medAmount, 1);


	  const bigNextPg = Math.min(currentPg + bigAmount, latestPg);
	  const bigBackPg = Math.max(currentPg - bigAmount, 1)
```

Then we use the id tags from our new html to retrieve the buttons and assign them to JavaScript variables:
```
	  const backMedTop = document.getElementById("backMedTop");
	  const backMedBottom = document.getElementById("backMedBottom");
	  const nextMedTop = document.getElementById("nextMedTop");   
	  const nextMedBottom = document.getElementById("nextMedBottom");

	  	
	  const backBig = document.getElementById("backBig");
	  const nextBig = document.getElementById("nextBig");
```

This utility function takes a page number and returns the full url corresponding to that page. We'll call it when constructing the `href` fields of our buttons:
```
	  function urlFormat(pgNum) {
	      return "http://testinglayout.cfw.me/comics/" + pgNum + "/";
	  }
```

Finally, we have the code that controls button visibility and `href` fields. The first `if` block hides the new back buttons if we are on the first page, and the second `if` block hides the new next buttons if we are on the latest page. The `else` blocks set the `href` fields to urls built from the page numbers that we calculated on lines 90 - 97.
```
	  if (currentPg == 1) {
	      backMedTop.style.display = "none";
	      backMedBottom.style.display = "none";
	      backBig.style.display = "none";
	  }
	  else {
	      backMedTop.setAttribute("href", urlFormat(medBackPg));
	      backMedBottom.setAttribute("href", urlFormat(medBackPg));
	      backBig.setAttribute("href", urlFormat(bigBackPg));
	  }
  
	  if (currentPg == latestPg) {
	      nextMedTop.style.display = "none";
	      nextMedBottom.style.display = "none";
	      nextBig.style.display = "none";
	  }
	  
	  else {
	      nextMedTop.setAttribute("href", urlFormat(medNextPg));
	      nextMedBottom.setAttribute("href", urlFormat(medNextPg));
	      nextBig.setAttribute("href", urlFormat(bigNextPg));
	  }
```  
