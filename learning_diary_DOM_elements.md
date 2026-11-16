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

### SIDE-Note textContent vs. innerHTML
`textContent` setzt den Inhalt als reinen Text, auch wenn der String HTML-Tags enthält (z. B. `<li>` würde als Text angezeigt, nicht als Element). `innerHTML` parst den String als HTML und baut daraus echte Elemente.

Deshalb wurde oben bei "ADD LIST ITEMS" bewusst `li.textContent` statt `li.innerHTML` verwendet: Bei Nutzereingaben öffnet `innerHTML` ein XSS-Risiko (eingeschleuster `<script>`- oder `<img onerror>`-Code würde ausgeführt). Faustregel: `textContent` für reinen Text, `innerHTML` nur für vertrauenswürdiges, selbst erzeugtes HTML.

```
jQuery
$("#myDiv").wrap('<div class="wrapper"></div>');

const element = document.querySelector("#myDiv");
const wrapper = document.createElement("div");

wrapper.classList.add("wrapper");
element.before(wrapper);
wrapper.append(element);

```

### FORM

Heute war ich an der Reihe, mich mit `<form>` auseinanderzusetzen - und ehrlich gesagt hat's mich kurz zurück in die jQuery-Zeit katapultiert. Bei jQuery hab ich `$("#meinForm").on("submit", ...)` praktisch im Schlaf geschrieben, musste mir hier aber erst wieder bewusst machen, dass `event.preventDefault()` nötig ist, sonst lädt die Seite stur neu. Klassischer Reflex-Fehler am Anfang.

```
// jQuery
$("#meinForm").on("submit", function (event) {
  event.preventDefault();
  const name = $("#name").val();
  console.log(name);
});
```

```
// Modernes JavaScript
const form = document.querySelector("#meinForm");

form.addEventListener("submit", function (event) {
  event.preventDefault();
  const name = form.elements.name.value;
  console.log(name);
});
```

Was mich überrascht hat: `form.elements` gibt mir Zugriff auf jedes Feld über seinen `name`-Attributwert, ohne dass ich jedes einzelne per `querySelector` suchen muss. Fühlt sich fast so bequem an wie jQuery, nur eben nativ.

```
form.elements.name;      // Input-Feld mit name="name"
form.elements["email"];  // alternative Schreibweise
form.reset();            // Formular zurücksetzen
form.checkValidity();    // HTML5-Validierung prüfen, ohne abzusenden
```

Richtig gefreut hab ich mich über `FormData` - das hätte mir früher bei jQuery-Projekten einiges an manuellem Zusammenklicken der Werte erspart:

```
form.addEventListener("submit", function (event) {
  event.preventDefault();
  const data = new FormData(form);
  console.log(data.get("name"));
});
```

Cool, `event.preventDefault()` taucht hier wieder auf - gleiche Regel wie oben, aber diesmal hab ich sie direkt erkannt, ohne nachdenken zu müssen. Fühlt sich schon ein bisschen nach Routine an.

### SIDE-Note preventDefault
Ohne `event.preventDefault()` macht der Browser bei `submit` das, was er seit den 90ern macht: Seite neu laden und Daten per GET/POST an die im `action`-Attribut angegebene URL schicken. Das wollte ich für mein Lernbeispiel nicht, deshalb der Reflex, das von Anfang an mitzudenken - genau wie bei `DOMContentLoaded` oben.

### BUTTON

Nach dem `<form>` war der `<button>` fast schon eine Erholung - hier hatte ich das Gefühl, dass sich vanilla JS und jQuery kaum unterscheiden. `.click()`-Handler fühlen sich in beiden Welten fast identisch an, nur die Syntax drumherum ist anders.

```
// jQuery
$("#meinButton").on("click", function () {
  $(this).prop("disabled", true);
});
```

```
// Modernes JavaScript
const button = document.querySelector("#meinButton");

button.addEventListener("click", function () {
  button.disabled = true;
});
```

Was mir aufgefallen ist: `disabled` ist bei vanilla JS einfach eine direkte Property auf dem Element, kein `.prop()` oder `.attr()` nötig. Weniger Indirektion, gefällt mir.

```
button.disabled;       // true/false - Button deaktivieren/aktivieren
button.type;           // "submit", "reset" oder "button"
button.textContent;    // Beschriftung des Buttons
button.form;           // referenziert das umgebende <form>-Element
button.click();        // Klick programmatisch auslösen
```

### SIDE-Note button type
Ein `<button>` ohne explizites `type`-Attribut ist standardmäßig `type="submit"` - innerhalb eines `<form>` löst ein Klick also automatisch ein Submit-Event aus. Das hat mich am Anfang überrascht, weil ich in jQuery-Projekten nie drüber nachdenken musste. Wenn ich nur einen reinen Aktions-Button ohne Submit-Verhalten will, brauche ich explizit `type="button"`.
