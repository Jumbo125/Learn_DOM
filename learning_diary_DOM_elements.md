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

### INPUT

Beim `<input>` hat's mich erwischt: Ich wollte reflexartig `$("#meinInput").val()` schreiben und musste mich bremsen. In jQuery war `.val()` meine Universal-Lösung für jedes Formularfeld, egal ob Text, Checkbox oder Radio. Vanilla JS zwingt mich, genauer hinzuschauen, welcher Input-Typ gerade vor mir liegt.

```
// jQuery
$("#meinInput").val();
$("#meinCheckbox").is(":checked");
```

```
// Modernes JavaScript
const input = document.querySelector("#meinInput");
input.value;

const checkbox = document.querySelector("#meinCheckbox");
checkbox.checked;
```

Das war mein Aha-Moment: `.val()` gibt es schlicht nicht - bei Text-Inputs heißt es `value`, bei Checkboxen und Radios `checked`. Zwei Properties statt einer Methode, aber dafür ist sofort klar, was gemeint ist.

```
input.value;      // aktueller Textwert
input.checked;    // bei checkbox/radio: true/false
input.type;       // "text", "email", "checkbox", "radio" ...
input.placeholder;// Platzhaltertext
input.disabled;   // Feld deaktivieren
input.focus();    // Fokus setzen
```

Außerdem live mitbekommen, dass es bei Eingabefeldern zwei Events gibt, die ich früher mit jQuery nicht bewusst unterschieden habe:

```
// feuert bei jedem Tastendruck / jeder Änderung sofort
input.addEventListener("input", function () {
  console.log(input.value);
});

// feuert erst, wenn das Feld den Fokus verliert
input.addEventListener("change", function () {
  console.log(input.value);
});
```

### SIDE-Note input vs. change
`input` reagiert auf jede einzelne Änderung in Echtzeit (gut für Live-Validierung oder Zeichenzähler), `change` erst, wenn das Feld seinen Wert committet und den Fokus verliert (gut für Checkboxen, Selects oder um nicht bei jedem Tastendruck eine teure Funktion auszulösen). Vorher war mir bei `$(...).on("change", ...)` in jQuery gar nicht bewusst, dass es diese zwei getrennten Signale überhaupt gibt.

### TABLE

Bei `<table>` dachte ich zuerst "ach, das ist doch nur ein weiteres Element mit Kindern" - und bin dann ziemlich schnell draufgekommen, dass Tabellen ihre eigenen, sehr praktischen Spezial-Methoden mitbringen, auf die ich bei jQuery nie geachtet habe, weil ich dort meist stur mit `.append()` gearbeitet hab.

```
// jQuery
$("#meineTabelle tbody").append("<tr><td>Neue Zeile</td></tr>");
```

```
// Modernes JavaScript
const table = document.querySelector("#meineTabelle");
const row = table.insertRow();
const cell = row.insertCell();
cell.textContent = "Neue Zeile";
```

Das hat mich überrascht: Ich muss gar nicht erst `createElement("tr")` und `createElement("td")` von Hand bauen und verschachteln - `insertRow()` und `insertCell()` machen das direkt am richtigen Platz in der Tabellenstruktur, inklusive automatischer Platzierung ans Ende.

```
table.rows;              // alle <tr>-Zeilen der Tabelle (live Collection)
table.rows[0].cells;     // alle <td>/<th>-Zellen dieser Zeile
table.tHead;             // referenziert <thead>, falls vorhanden
table.tBodies;           // alle <tbody>-Elemente
table.insertRow(0);      // neue Zeile an Position 0 einfügen
table.deleteRow(0);      // Zeile an Position 0 löschen
row.insertCell(-1);      // neue Zelle ans Ende der Zeile anhängen
```

### SIDE-Note live Collections
`table.rows` ist eine `HTMLCollection`, keine normale Array - und sie ist "live", das heißt sie aktualisiert sich automatisch, wenn ich Zeilen hinzufüge oder lösche. Das hat mich kurz verwirrt, weil ich aus jQuery gewohnt war, dass `$(...)` immer eine feste Momentaufnahme liefert. Für eine `for`-Schleife, in der ich gleichzeitig Zeilen lösche, ist das eine Falle: die Collection schrumpft währenddessen mit, und ich überspringe Elemente, wenn ich nicht rückwärts iteriere oder vorher in ein echtes Array kopiere (`Array.from(table.rows)`).

### UL

Nach den `<table>`-Spezial-Methoden war `<ul>` fast ernüchternd einfach - hier gibt's keine `insertRow()`-Magie, es ist wieder ganz normales Kinder-Handling wie beim ersten "ADD LIST ITEMS"-Beispiel ganz oben. Gut zu merken: eine `<ul>` selbst ist nichts Besonderes, interessant wird's erst bei den `<li>`-Kindern.

```
// jQuery
$("#meineListe").append("<li>Neuer Punkt</li>");
$("#meineListe li").eq(1).remove();
```

```
// Modernes JavaScript
const list = document.querySelector("#meineListe");

const li = document.createElement("li");
li.textContent = "Neuer Punkt";
list.append(li);

list.children[1].remove();
```

Was mir hier wieder aufgefallen ist: `.eq(1)` aus jQuery (das n-te Element einer Auswahl) hat keinen direkten Methoden-Ersatz, ich greife einfach über `list.children[1]` auf den Index zu - kein Umweg über eine jQuery-Wrapper-Methode nötig, einfacher Array-Zugriff reicht.

```
list.children;          // alle direkten <li>-Kinder (live HTMLCollection)
list.children.length;   // Anzahl der Listenpunkte
list.firstElementChild; // erster <li>-Punkt
list.lastElementChild;  // letzter <li>-Punkt
list.querySelectorAll("li.done"); // nur abgehakte Punkte, z. B. Todo-Liste
```

### SIDE-Note ul vs. ol
`<ul>` und `<ol>` teilen sich dieselbe DOM-API (`children`, `append`, etc.) - der einzige Unterschied liegt in der Semantik und im `start`/`type`-Attribut von `<ol>` für nummerierte Listen. Gut zu wissen, falls ich mal zwischen beiden wechsle: am JS-Code ändert sich dabei nichts.
