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

## Formulare

Bei Formularen ist der Unterschied zu jQuery vor allem, wie ich an die Elemente und deren Werte komme. Das Submit-Event bleibt im Prinzip gleich. `preventDefault()` verhindert dabei das normale Absenden des Formulars, wenn ich die Daten zuerst mit JavaScript verarbeiten möchte.

```js
// jQuery
$("#meinForm").on("submit", function (event) {
  event.preventDefault();
  const name = $("#name").val();
  console.log(name);
});
```

Ohne jQuery:

```js
const form = document.querySelector("#meinForm");

form.addEventListener("submit", function (event) {
  event.preventDefault();
  const name = form.elements.name.value;
  console.log(name);
});
```

Mit `form.elements` kann ich direkt über das `name`-Attribut auf die Eingabefelder zugreifen. Ich muss also nicht zwingend jedes Feld extra selektieren.

```js
form.elements.name;
form.elements["email"];
form.reset();
form.checkValidity();
```

Für mehrere Werte gibt es außerdem `FormData`:

```js
form.addEventListener("submit", function (event) {
  event.preventDefault();
  const data = new FormData(form);
  console.log(data.get("name"));
});
```

## Button

Einen Klick registriere ich jetzt über `addEventListener`. Eine Eigenschaft wie `disabled` kann ich danach direkt setzen.

```js
// jQuery
$("#meinButton").on("click", function () {
  $(this).prop("disabled", true);
});

// JavaScript
const button = document.querySelector("#meinButton");
button.addEventListener("click", function () {
  button.disabled = true;
});
```

Weitere Eigenschaften bzw. Methoden eines Buttons:

```js
button.disabled;     // aktiviert/deaktiviert
button.type;         // submit, reset oder button
button.textContent;  // Text
button.form;         // zugehöriges Formular
button.click();      // Klick per Code auslösen
```

Innerhalb eines Formulars sollte ich bei einem normalen Aktionsbutton `type="button"` angeben. Sonst wird standardmäßig ein Submit ausgelöst.

## Input

Hier verwende ich statt `.val()` einfach `.value`. Bei Checkboxen oder Radio-Buttons ist dagegen `.checked` relevant.

```js
// jQuery
$("#meinInput").val();
$("#meinCheckbox").is(":checked");

// JavaScript
const input = document.querySelector("#meinInput");
console.log(input.value);

const checkbox = document.querySelector("#meinCheckbox");
console.log(checkbox.checked);
```

Ein paar nützliche Eigenschaften:

```js
input.value;
input.checked;
input.type;
input.placeholder;
input.disabled;
input.focus();
```

Bei den Events muss ich zwischen `input` und `change` unterscheiden. `input` reagiert normalerweise sofort auf Änderungen, `change` erst, wenn eine Änderung übernommen wurde (bei Texteingaben üblicherweise beim Verlassen des Feldes).

```js
input.addEventListener("input", function () {
  console.log(input.value);
});

input.addEventListener("change", function () {
  console.log(input.value);
});
```

Das kann z. B. bei einer Live-Suche wichtig sein, wo ich schon während des Tippens reagieren möchte.

## Tabelle

Für Tabellen gibt es eigene Methoden zum Erstellen oder Löschen von Zeilen. In jQuery habe ich dafür meist HTML angehängt.

```js
// jQuery
$("#meineTabelle tbody").append("<tr><td>Neue Zeile</td></tr>");

// JavaScript
const table = document.querySelector("#meineTabelle");
const row = table.insertRow();
const cell = row.insertCell();
cell.textContent = "Neue Zeile";
```

Mit `insertRow()` wird eine Zeile hinzugefügt und mit `insertCell()` eine Zelle in dieser Zeile. Die wichtigsten Zugriffe habe ich mir so notiert:

```js
table.rows;               // Zeilen
table.rows[0].cells;      // Zellen der ersten Zeile
table.tHead;
table.tBodies;
table.insertRow(0);       // ganz am Anfang
table.deleteRow(0);       // erste Zeile löschen
row.insertCell(-1);       // Zelle hinten anhängen
```

`table.rows` aktualisiert sich übrigens beim Ändern der Tabelle automatisch. Das muss man beachten, wenn man während einer Schleife Zeilen löscht.

## UL und LI

Bei Listen reicht die normale DOM-Manipulation, also ein Element erstellen und dann anhängen. Ein spezielles `insertRow()` wie bei Tabellen gibt es nicht.

```js
// jQuery
$("#meineListe").append("<li>Neuer Punkt</li>");
$("#meineListe li").eq(1).remove();
```

```js
const list = document.querySelector("#meineListe");
const li = document.createElement("li");
li.textContent = "Neuer Punkt";
list.append(li);

list.children[1].remove();
```

Über `children` kann ich also auch auf einzelne Listenelemente zugreifen. Die Zählung beginnt bei 0.

```js
list.children;
list.children.length;
list.firstElementChild;
list.lastElementChild;
list.querySelectorAll("li.done");
```

Eine `ol` funktioniert in JavaScript grundsätzlich genauso, nur die Darstellung bzw. Bedeutung der Liste ist anders.

## Select / Option

Ein Select-Feld hat wie ein normales Textfeld eine `value`-Eigenschaft. So kann ich eine Auswahl lesen oder ändern.

```js
// jQuery
$("#meinSelect").val();
$("#meinSelect").val("de");

// JavaScript
const select = document.querySelector("#meinSelect");
console.log(select.value);
select.value = "de";
```

Neue Einträge lassen sich entweder mit `createElement` oder etwas kürzer mit `new Option()` hinzufügen.

```js
// jQuery
$("#meinSelect").append('<option value="at">Österreich</option>');

// JavaScript
const option = document.createElement("option");
option.value = "at";
option.textContent = "Österreich";
select.append(option);

// kürzere Variante
const option2 = new Option("Österreich", "at");
select.append(option2);
```

Dazu passen folgende Eigenschaften:

```js
select.options;
select.selectedIndex;
select.selectedOptions;
select.options[0].value;
select.options[0].selected;
```

Bei `multiple` kann ich mehrere Einträge auswählen. `select.value` liefert dabei nicht alle Werte, dafür gibt es `selectedOptions`.

## Canvas

Canvas ist etwas anders als die bisherigen HTML-Elemente. Über JavaScript zeichne ich auf einer Fläche, dafür brauche ich zuerst den 2D-Kontext. Einen eigenen jQuery-Ersatz gibt es dafür nicht wirklich.

```js
const canvas = document.querySelector("#meineCanvas");
const ctx = canvas.getContext("2d");

ctx.fillStyle = "steelblue";
ctx.fillRect(10, 10, 100, 50);

ctx.beginPath();
ctx.arc(150, 75, 40, 0, Math.PI * 2);
ctx.fillStyle = "tomato";
ctx.fill();
```

Mit `fillRect()` wird hier ein Rechteck gezeichnet, danach ein Kreis mit `arc()`.

```js
canvas.width;
canvas.height;
ctx.clearRect(0, 0, canvas.width, canvas.height);
ctx.fillText("Hallo", 10, 100);
canvas.toDataURL();
```

Wichtig ist noch die Unterscheidung zwischen `canvas.width`/`canvas.height` und der Größe über CSS. Die ersten beiden Werte bestimmen die eigentliche Zeichenauflösung. Über CSS kann ich die Fläche zwar größer anzeigen, die Zeichnung wird dadurch aber nicht automatisch schärfer.
