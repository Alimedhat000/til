#### 1. Basic Selectors
1. Universal `$('*')`
2. ID `$('#id')`
3. Class `$('.class')`
4. Element `$("eleement")`
5. Multiple `$("selector1, selector2, selector3")` 
#### 2. Hierarchy Selectors
1. Child `$("parent > child")`:
		selects all direct elements specified by the selector `child` of element specified by the selector `parent`
2. Descendant `$("parent > child")`
		selects all descendants of the selector `child` of `parent`
3. Adjacent sibling `$(prev + next)`
4. Next siblings `$(prev ~ siblings)`
		Selects all sibling elements that follow after the “prev” element, have the same parent, and match the filtering “siblings” selector.
#### 3. Basic filter
1. `$("selector:first")` 
2. `$("selector:first-child")` 
3. `$("selector:last")`
4. `$("selector:last-child")`
5. `$("selector:lth()")`
6. `$("selector:nth-child()")`
7. `$("selector:not()")`

#### 4. Attribute
1. Attribute Contains Prefix Selector `[name|="value"]`
	Selects elements that have the specified attribute with a value either exactly equal to a given string or starting with that string followed by a hyphen (-).

2. Attribute Contains Selector `[name*="value"]`
	Selects elements that have the specified attribute with a value containing a given substring.

3. Attribute Contains Word Selector `[name~="value"]`
	Selects elements that have the specified attribute with a value containing a given word, delimited by spaces.

4. Attribute Ends With Selector `[name$="value"]`
	Selects elements that have the specified attribute with a value ending exactly with a given string. The comparison is case sensitive.

5. Attribute Equals Selector `[name="value"]`
	Selects elements that have the specified attribute with a value exactly equal to a certain value.

6. Attribute Not Equal Selector `[name!="value"]`
	Selects elements that either don’t have the specified attribute, or do have the specified attribute but not with a certain value.

7. Attribute Starts With Selector `[name^="value"]`
	Selects elements that have the specified attribute with a value beginning exactly with a given string.

8. Has Attribute Selector `[name]`
	Selects elements that have the specified attribute, with any value.

9. Multiple Attribute Selector `[name="value"][name2="value2"]`
	Selects elements that match multiple attribute conditions at once.
#### 5. DOM Traversal 

1. `.parent()` - Selects the immediate parent of the specified element.
    
2. `.parents()` - Selects all ancestor elements of the specified element.
    
3. `.parentsUntil(selector)` - Selects all ancestor elements between the specified element and the given selector.
    
4. `.children()` - Selects all immediate child elements of the specified element.
    
5. `.find(selector)` - Finds all descendant elements that match the given selector.
    
6. `.siblings()` - Selects all sibling elements of the specified element.
    
7. `.next()` - Selects the immediate next sibling of the specified element.
    
8. `.nextAll()` - Selects all next sibling elements after the specified element.
    
9. `.nextUntil(selector)` - Selects all next sibling elements between the specified element and the given selector.
    
10. `.prev()` - Selects the immediate previous sibling of the specified element.
    
11. `.prevAll()` - Selects all previous sibling elements before the specified element.
    
12. `.prevUntil(selector)` - Selects all previous sibling elements between the specified element and the given selector.
    
13. `.first()` - Selects the first element in the specified set.
    
14. `.last()` - Selects the last element in the specified set.
    
15. `.eq(index)` - Selects the element at the specified index.
    
16. `.filter(selector)` - Filters elements based on the given selector.
    
17. `.not(selector)` - Excludes elements that match the given selector.

18. `.is(selector)` - bool: Checks if elements match a selector. 

19. `.has(selector)` bool: Checks if an element contains a certain child element.

20. `.each(<callback function>)` - Iterates over elements and calls the callback for each of them while passing each element as a parameter. The first argument is the index of the element being processed and the second is the DOM element (not the object) itself. ex:
```js
$("table tr").each(function(i, tr) { $(tr).find(’td’).each(function(j, td) { console.log(‘Row ${i} Column ${j}: ${$(td).text()}‘) }) });
```

#### 6. DOM Manipulation
1. `.addClass()` `.hasClass()` and `.removeClass()` 
2. `.after()` and `.before()`
3. `.append()` and `.prepend()`
4. `.remove()`
5. `.clone()`
6. `.html()`
7. `.css()`
8. `.val()`

#### 7. Events
1. `click(<CallBack>)`
2. `dblclick(<CallBack>)`
3. `hover(<CallBack>)`
4. `mouseenter()` and `mouseleave()`
5. `keydown()` and `keyup()` 
6. `focus()` and `blur()`
7. `submit()`
8. `on(event,selector,callback)` Used to attach multiple event handlers dynamically.
9. off(event) removes event handler for `event`.

#### 8. AJAX
1. Simple AJAX Request Using `$.ajax()`
	The `$.ajax()` method is the most flexible way to send requests to the server.
``` js
$.ajax({
    url: "https://api.example.com/data",
    type: "GET",
    dataType: "json",
    success: function(response) {
        console.log("Data received:", response);
    },
    error: function(xhr, status, error) {
        console.error("Error:", error);
    },
	complete: function(xhr) { 
	 console.log("Request completed");
	},
	statusCode: {
		404: function() {
			console.log("Error 404: Not Found");
		}
	}
});
```

2. Fetching Real-Time Data with `$.get()`
	A simpler way to retrieve data using a GET request.
```js
$.get("https://api.example.com/data", function(response) {
    console.log(response);
});
```

3. Sending Data with `$.post()`
	The `$.post()` method is used for sending data to a server.
```js
$.post("https://api.example.com/submit", 
	{ name: "Ali", age: 25 },
	function(response) {
		console.log("Server Response:", response);
	}
);
```
4. Fetching JSON Data with `$.getJSON()` 
```js
$.getJSON("https://api.example.com/users", function(users) {
    $.each(users, function(index, user) {
        $("#userList").append("<li>" + user.name + "</li>");
    });
});
```
5. Loading Content into a Page with `$.load()`
 If you need to fetch and insert content into a page dynamically:
 ```js
 $("#content").load("data.html");
```