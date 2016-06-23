## Use Aurelia to build Electron apps.

This repo is the result of setting up a development environment for Aurelia and Electron. It contains the bits needed both for debugging and reloading the app during development. 

It is based upon the typescript skeleton app provided by the Aurelia team. 

## Setting up 

```
npm install 
npm install -g jspm 
npm install -g gulp
jspm install -y
typings install 
```



## Run the app

```shell
gulp watch
```

You should now be able to make changes to source and have the app reload when the changes are saved. If you need to debug you can simply attach the debugger from VS Code. Don't care how it works? Stop reading.

## Automatic reloading 

The first challenge was to allow the application to reload when something is changed in the source (html, css or typescript files). This actually turned out to be quite easy as it is already an NPM package for this called *electron-connect*. 

After installing, I just had to make a few changes to the watch.js gulp file that came with the skeleton app.

```javascript
var gulp = require('gulp');
var paths = require('../paths');
//var browserSync = require('browser-sync');
var electron = require('electron-connect').server.create();
// outputs changes to files to the console
function reportChange(event) {
  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
}

// this task wil watch for changes
// to js, html, and css files and call the
// reportChange method. Also, by depending on the
// serve task, it will instantiate a browserSync session
gulp.task('watch', ['build'],function() {
  
   // Start browser process and enable remote debugging.
  electron.start("--remote-debugging-port=9223");

  // Restart browser process
  gulp.watch('index.js', electron.restart);

  // Reload renderer process  
  gulp.watch(paths.source, ['build-system', electron.reload]).on('change', reportChange);
  gulp.watch(paths.html, ['build-html', electron.reload]).on('change', reportChange);
  gulp.watch(paths.css, ['build-css', electron.reload]).on('change', reportChange);
  gulp.watch(paths.style, electron.reload).on('change', reportChange);
});

```

So instead of relying on BrowserSync we simply ask the Electron app to reload whenever something changes. 

In addition to this we also pass the "--remote-debugging-port=9223" to the *start* method to allow the app to be debugged.


## Debugging

Being able to debug is now a simple matter of installing the Chrome Debugger Extension and adding a launch.json file to the project in VS Code. 

```java
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach with sourcemaps",
            "type": "chrome",
            "request": "attach",
            "port": 9223,
            "sourceMaps": true,
            "webRoot": "${workspaceRoot}"
        }
    ]
}
```

> I could not get port 9222 to work on Windows so I simply changed the debug port to 9223. 
  




