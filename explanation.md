# Pro Editor: Technical Line-by-Line Breakdown

This document provides a detailed explanation of how the Pro Editor URI functions, broken down by its three core components: HTML, CSS, and JavaScript.

## 1. The Global Shell
```html
data:text/html,
<title>Pro Editor</title>
<body style="margin:0;display:flex;flex-direction:column;height:100vh;font-family:sans-serif;background:#f4f4f9;">
```
* **`data:text/html,`**: The protocol that tells the browser this isn't a link to a file, but the file itself.
* **`<title>`**: Sets the text displayed on the browser tab.
* **`margin:0`**: Removes the default white border around the page.
* **`display:flex; flex-direction:column;`**: Activates Flexbox and stacks the header, editor, and toolbar vertically.
* **`height:100vh`**: Forces the page to be exactly the height of the screen (100% of the Viewport Height).

## 2. The Header (Identity & Primary Actions)
```html
<div style="background:#007bff;padding:10px;display:flex;justify-content:space-between;align-items:center;">
```
* **`background:#007bff`**: The signature blue brand color.
* **`justify-content:space-between`**: Pushes the Title to the far left and the Buttons to the far right.

```html
<button onclick="document.getElementById('m').style.display='flex'">Info</button>
```
* **`onclick`**: A JavaScript trigger. When clicked, it finds the element with ID 'm' (the hidden Info modal) and changes its display from 'none' to 'flex' so it becomes visible.

## 3. The Formatting Toolbar
```html
<div style="padding:8px;display:flex;overflow-x:auto;white-space:nowrap;gap:6px;background:#444;">
```
* **`overflow-x:auto`**: Allows you to swipe left/right on the toolbar if your phone screen is too narrow to show all buttons.
* **`white-space:nowrap`**: Prevents buttons from jumping to a second line.

```html
<button onclick="document.execCommand('bold')">B</button>
```
* **`document.execCommand`**: A built-in browser command that applies styles (Bold, Italic, etc.) to the currently selected text or cursor position.

## 4. The Typing Area (The Engine)
```html
<div id="edit" contenteditable onfocus="if(this.innerText==='Type here...'){this.innerText='';}"
     style="flex:1 1 auto;font-size:2rem;line-height:1.5;max-width:60rem;width:100%;margin:0 auto;padding:2rem;outline:none;background:white;overflow-y:auto;box-sizing:border-box;">
  Type here...
</div>
```
* **`contenteditable`**: The attribute that turns a normal `<div>` into a fully functional text editor.
* **`onfocus`**: If the text matches the placeholder "Type here...", it clears it as soon as you tap the area.
* **`flex:1 1 auto`**: Tells this section to grow and take up all remaining space between the header and toolbar.
* **`overflow-y:auto`**: Adds a scrollbar only to the text area when the content gets long, keeping the toolbar visible.

## 5. The Markdown Conversion Logic
```javascript
function cp(mode){
  const el=document.getElementById('edit');
  let t=el.innerHTML;
  if(mode==='md'){
    t=t.replace(/<(b|strong)>(.*?)<\/\1>/gi,'**$2**')
       .replace(/<(i|em)>(.*?)<\/\1>/gi,'*$2*')
       .replace(/<(s|strike|del)>(.*?)<\/\1>/gi,'~~$2~~')
       .replace(/<u>(.*?)<\/u>/gi,'_$1_')
       .replace(/<h1>(.*?)<\/h1>/gi,'# $1\n')
       .replace(/<li>(.*?)<\/li>/gi,'- $1\n');
  }
```
* **`innerHTML`**: Grabs the text *including* the hidden HTML tags (like `<b>`).
* **`.replace()`**: Uses Regular Expressions (Regex) to find HTML tags and swap them for Markdown symbols.
* **`gi`**: A flag meaning "Global" (replace all) and "Insensitive" (ignore capital letters).

## 6. The Copy Mechanism
```javascript
const a=document.createElement('textarea');
a.value=t.trim();
document.body.appendChild(a);
a.select();
document.execCommand('copy');
document.body.removeChild(a);
```
* **`createElement('textarea')`**: Creates a hidden text box.
* **`a.select()`**: Highlights the text inside that hidden box.
* **`document.execCommand('copy')`**: Triggers the system's "Copy" function. This older method is used instead of the new Clipboard API because it doesn't require HTTPS.

