# DOM Manipulation lernen

## Document Variable 
Ich kämpfe mit dem Umstieg von jQuery zu ECMAScript 2015 basierten Eventlistner.

Variable: Document ist gleich geblieben. Es symbolisert das HTML-DOM als ganzes.

Es ist ein Object Typ, hat keine Attribute, wie id oder class usw

### jquery
Statt wie bisher:
```$(document).ready(function () {
  // DOM ist bereit
});
```

### ES6

```
document.addEventListener("DOMContentLoaded", function () {
  // DOM ist bereit
}); 
```

### Side-Note Defer



### Eigenschaften der Document Objekt
```
document.title          // Titel der Seite
document.URL            // aktuelle URL
document.location       // Adresse/Navigation
document.body           // <body>-Element
document.head           // <head>-Element
document.documentElement // <html>-Element
document.cookie         // Cookies
document.forms          // alle <form>-Elemente
document.images         // alle <img>-Elemente
```

### Methoden
```
document.getElementById("save");
document.querySelector(".button");
document.querySelectorAll("li");
document.createElement("div");
document.addEventListener("click", handler);
```
###  SIDE-Note DEFER
defer lädt ein externes JavaScript parallel zum HTML, ohne das Laden der Seite zu blockieren. Es führt das Script erst aus, nachdem das HTML-DOM aufgebaut wurde.

## Manipulation via id and class

Früher wurde mit $(id) oder $(class) gearbeitet. Wie macht man das nun?

### Selektieren

#### ID
```
//jQuery
$("#meinId");
```

```
// Modernes JavaScript
document.querySelector("#meinId");
```

#### Class
```
// jQuery
$(".meineKlasse");
```

```
// Modernes JavaScript: erstes passendes Element
document.querySelector(".meineKlasse");
```

#### Alle Elemente selekiteren
```
// jQuery
$(".meineKlasse");

// Modernes JavaScript: alle passenden Elemente
document.querySelectorAll(".meineKlasse");
```

### Children
```
// Alle .item irgendwo innerhalb von #menu
document.querySelectorAll("#menu .item");

// Nur direkte li-Kinder von #menu
document.querySelectorAll("#menu > li");

// Erstes direktes li-Kind
document.querySelector("#menu > li");
```

### ADD LIST ITEMS

```
$("#myList").append("<li>Item 4</li>");
```

```
const li = document.createElement("li");
li.textContent = "Item 4";
document.querySelector("#myList").append(li);
```

## HTML TAGS

### DIV

```
const div = document.querySelector("#myDiv");

div.id;           // die ID, z. B. "myDiv"
div.className;    // CSS-Klassen als Text
div.classList;    // CSS-Klassen verwalten: add(), remove(), toggle()
div.textContent;  // reiner Textinhalt
div.innerHTML;    // Inhalt als HTML
div.style;        // Inline-CSS lesen oder setzen
div.children;     // direkte Kindelemente
div.parentElement;// Elternelement
div.dataset;      // data-Attribute, z. B. data-user-id
div.hidden;       // Element verstecken/anzeigen
```

```
jQuery
$("#myDiv").wrap('<div class="wrapper"></div>');

const element = document.querySelector("#myDiv");
const wrapper = document.createElement("div");

wrapper.classList.add("wrapper");
element.before(wrapper);
wrapper.append(element);

```
