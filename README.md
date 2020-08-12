# html-include

There's still no standard mechanism for client side includes in HTML. Here's a fetch/promise based solution that seems to work in any modern browser:

The App class is just an example, put the function wherever you want.

```javascript
export default class App {
    process_includes() {
        const includes = Array.from(document.querySelectorAll('[html-include]'));
        const promises = includes.map(el => {
            const endpoint = `${el.getAttribute('html-include')}`;
            return fetch(endpoint).then(response => response.text());
        });

        return Promise.all(promises).then((values) => {
            for (let i = 0; i < includes.length; i++) {
                includes[i].outerHTML = values[i];
            }
            if (document.querySelectorAll('[html-include]').length > 0) {
                this.process_includes();
            }
        });
    }
}
```

It works recursively, i.e. your included HTML files can have their own includes.

Example usage:

```HTML
<html>
<head>
    <script type="module">
        import App from '/app.js';
        const app = new App();

        // No need for jQuery, just what's used in the example to run things after page load
        jQuery(() => {
            app.process_includes();
        });
    </script>
</head>

<body>
    <div html-include="pages/header.html"></div>
    <div html-include="pages/contents.html"></div>
    <div html-include="pages/footer.html"></div>
</body>
</html>
 ```
