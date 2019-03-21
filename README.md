# cordova-plugin-hatsa-openwith
Cordova plugin voor open-met-functionaliteit op Android, en enkel op Android en dus NIET op iOS

**Opmerking:** Ik heb alle bestanden die volgens mij bij iOS horen eruit gehaald, en eveneens uit `plugin.xml`. 

**LET OP:** Het bestand `AbstractOpenwithActivity.java` moet aangepast worden zodat er in dit bestand verwezen wordt naar de 
`MainActivity` van onze app. Er staat nu nog `com.example.hello.MainActivity.class` maar `com.example.hello` is de 
naam van het hello-world-project.

**BRON:** [https://github.com/nyholmsolutions/cordova-plugin-openwith](https://github.com/nyholmsolutions/cordova-plugin-openwith)

# Usage
In de `www/js` map van je project, voeg deze funtie toe aan `index.js`.

```
function setupOpenwith() {

  // Increase verbosity if you need more logs
  //cordova.openwith.setVerbosity(cordova.openwith.DEBUG);

  // Initialize the plugin
  cordova.openwith.init(initSuccess, initError);

  function initSuccess()  { console.log('init success!'); }
  function initError(err) { console.log('init failed: ' + err); }

  // Define your file handler
  cordova.openwith.addHandler(myHandler);

  function myHandler(intent) {
    console.log('intent received');

    console.log('  action: ' + intent.action); // type of action requested by the user
    console.log('  exit: ' + intent.exit); // if true, you should exit the app after processing

    for (var i = 0; i < intent.items.length; ++i) {
      var item = intent.items[i];
      console.log('  type: ', item.type);   // mime type
      console.log('  uri:  ', item.uri);     // uri to the file, probably NOT a web uri

      // some optional additional info
      console.log('  text: ', item.text);   // text to share alongside the item, iOS only
      console.log('  name: ', item.name);   // suggested name of the image, iOS 11+ only
      console.log('  utis: ', item.utis);
      console.log('  path: ', item.path);   // path on the device, generally undefined
    }

    // ...
    // Here, you probably want to do something useful with the data
    // ...
    // An example...

    if (intent.items.length > 0) {
      cordova.openwith.load(intent.items[0], function(data, item) {

        // data is a long base64 string with the content of the file
        console.log("the item weights " + data.length + " bytes");
        uploadToServer(item);

        // "exit" when done.
        // Note that there is no need to wait for the upload to finish,
        // the app can continue while in background.
        if (intent.exit) { cordova.openwith.exit(); }
      });
    }
    else {
      if (intent.exit) { cordova.openwith.exit(); }
    }
  }
}
```

Voer de functie uit op het event `deviceready`.

```
var app = {
    
    ...
    
    onDeviceReady: function() {
        
        ...
        
        setupOpenwith();
    },

    ...
    
};
```