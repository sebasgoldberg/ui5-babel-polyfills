# Polyfill External Lib
In this library it is encapsulated an external library: babel polyfills (support for async/await).

## Code For Reuse The Library

Here are detailed examples about how to use the library in a UI5 project.

- In your project: `npm i ui5-babel-polyfills`

- `./webapp/index.html`:
This is only for test environment, you do not need this in SAP Gateway Server.
```html
    <!-- Here we told told to SAPUI5 where it can find our library resources using the attribute data-sap-ui-resourceroots -->
    <!-- Note that the library is loaded at bootstrap with attribute data-sap-ui-libs -->
    <script id="sap-ui-bootstrap"
        src="https://sapui5.hana.ondemand.com/1.52.13/resources/sap-ui-core.js"
        data-sap-ui-libs="sap.m,custom.libs.external.polyfill"
        data-sap-ui-theme="sap_belize"
        data-sap-ui-compatVersion="edge"
        data-sap-ui-preload="async"
        data-sap-ui-resourceroots='{
            "sap.ui.demo.basicTemplate": "./",
            "custom.libs.external.polyfill": "resources/ui5-babel-polyfills/dist/"
            }'>
    </script>
```

Obiously `resources` was mapped with node_modules folder in `Grunfile.js`.
```
        openui5_connect: {
            options: {
                resources: [
                    "../node_modules/",
                ]
            },
```


- `./manifest.json`: This is the important part. We indicate our custom library as dependency.
```json
    "sap.ui5": {
        "dependencies": {
            "libs": {
                "<other library>": {},
                "custom.libs.external.polyfill": {}
            }
        }
    }
```

- Now we can use async/await syntax in our project.


## Some Coments About The Implementation
- `webapp/library.js`: Basically defines and initialize the library:
```javascript
/*eslint no-unused-vars: ["error", { "varsIgnorePattern": "polyfill" }]*/
import polyfill from "./lib/polyfill"; // Here is where are imported the babel polyfills resources.

let oLibrary = sap.ui.getCore().initLibrary({
    name: "custom.libs.external.polyfill",
    noLibraryCSS: true,
});

export default oLibrary;
```

- `manifest.json`: Here we tell to UI5 that we are defining a library.
```json
    "sap.app": {
        "id": "custom.libs.external.polyfill",
        "type": "library"
    }
```

- `Gruntfile.js`: Here we tell to openui5_preload task that we are defining a library (this is for the library-preload.js bundle creation).
```javascript
    openui5_preload: {
        component: {
            options: {
                resources: {
                    cwd: "transp",
                    prefix: "custom/libs/external/polyfill"
                },
                dest: "<%= dir.dist %>",
                compatVersion: "1.52",
            },
            libraries: true
        }
    },
```

## Getting Started DEV

1.Install node.js (get it from [nodejs.org](http://nodejs.org/)).
  * If working behind a proxy, you need to configure it properly (HTTP_PROXY / HTTPS_PROXY / NO_PROXY environment variables)

2.Install grunt-cli globally

```sh
npm install grunt-cli -g
```

3.Clone the repository and navigate into it

```sh
git clone https://github.com/sebasgoldberg/ui5-babel-polyfills.git
cd ui5-babel-polyfills
```

4.Install all npm dependencies

```sh
npm install
```

5.Run grunt to lint, build and run a local server (have a look into `Gruntfile.js` to see all the tasks).

```sh
grunt
```

