# meteor-loggly-demos  

Every few years, it's useful to survey the package ecosystem and figure out the state of devops logging.  Logging in of itself isn't particularly challenging; the industry has been doing it for 50 years.  The challenges are well know.  It's mostly just a matter of new operating systems, new businesses, maybe a new protocol or file format.  Typically, it's not something that needs to be done more than once every two years; and this is probably the third time I've done it for the Meteor ecosystem.  

Since the last time I took a close look at the logging landscape, Meteor has seen a lot of changes...  ES2015 has introduced new syntax, Meteor 1.2 introduced NPM packages, and Meteor 1.3 introduced import statements.  So a lot of the old packages break with current Meteor apps; and old tutorials are out of date.  


### Methodology  

After talking with some other devs, reading tutorials, and searching the Meteor forums, I narrowed the scope of my survey to the following 7 packages.   

**npm packages**  
```json
{
  "dependencies": {
    "loggly": "^1.1.1",
    "winston": "^2.3.1",
    "winston-loggly-bulk": "^1.4.2"
  }
}
```

**npm packages** 
```sh
miktam:loggly 
infinitedg:winston-loggly
infinitedg:winston
clinical:winston-browser-logging
```

I actually started off with the `brentjanderson:meteor-winston-client` package, but it needed a bit of rewrite, so it eventually became `clinical:winston-browser-logging`.  Otherwise, it was simply a matter of following tutorials and READMES, creating some demos, and figuring out which ones still worked and were up to date. 


### Reference Builds    
There were two main questions that needed to be resolved:  NPM or Atmosphere?  And just Loggly or should the entire Winston logging layer be included?  Here are the results of my little experiment.


#### miktam-loggly  

```sh
# atmosphere packages
miktam:loggly  
```

_pros_  
Simplest library to use to get Loggly support on both client/server.  Configuration is done via Meteor.settings file. Isomorphic API.  Echos logs to server/browser console.

_cons_  
Requires Meteor/Atmosphere package infrastructure.  


#### meteor-winston  

```sh
# atmosphere packages
clinical:winston-browser-logging
infinitedg:winston-loggly
infinitedg:winston
```

_pros_  
The oldest Winston/Loggly integration, with support for isomorphic APIs, browser side logging, and a complete Winston infrastructure.  

_cons_  
Nonstandard capitalization and naming conventions.  Requires multiple packages. Has overhead of Winston transport infrastructure. Requires Meteor/Atmosphere package infrastructure & bundling.  Wrapped NPM packages cause delays in upgrades.  Uses globals.  


#### winston-loggly-bulk-demo  

```sh
# npm packages
"winston": "^2.3.1"
"winston-loggly-bulk": "^1.4.2"

# atmosphere packages
clinical:winston-browser-logging
```

_pros_  
Loggly recommended implementation for Node.js apps.  Echos to server console.  Bulk logging.  Isomorphic API.  

_cons_  
Doesn't support browser side logging.  


#### loggly-demo  

```sh
# npm packages
"loggly": "^1.1.1"
```

_pros_  
Cleanest NPM integration with the best ES2015 support.  

_cons_  
No support for logging from the client. Logging doesn't echo to the server console.  Doesn't use globals.  Very secure.  The default loggly npm package is very quiet; and basically acts as a background process to securely get server events from your app to the Loggly servers.  


### Architecture Decision    
Cool.  We actually have _four_ different ways to connect our Meteor app to Loggly.  So, which one would be best for a production system?  

Well, first of all, we look at the following packages, and note that most of them are 4 years old, are basically just wrapper packages around NPM packages, are not maintained, or have been superceded by newer libraries.  Unless you have a ~METEOR@1.0.2 app, a packages-only app, or are running a local copy of Fastosphere, there's not a lot of reason to use these older packages.  Of the four demos, `meteor-winston` is a classic, but it's also falling behind as the rest of the technology ecosystem changes.  

```sh
infinitedg:winston
infinitedg:winston-loggly
brentjanderson:meteor-winston-client
```

Of the remaining architectures, the next question is whether or not you want a pure NPM package to just act as a behind-the-scenes reporting tool. If any of the following apply to you, you may want to look at the `loggly` npm package and the `loggly-demo`, and leave it at that.

- [ ] need SNMP-like functionality on a cluster of servers
- [ ] need quite statistics reporting from desktop electron apps
- [ ] have a strict NPM-only policy
- [ ] have an express or apollo configuration 
- [ ] aren't using minimongo or DDP

Assuming you're starting a new Meteor app, using the latest ES2015 tutorials with `import` statements, and don't have any exotic requirements around which packages you use or what your client side looks like, then the easiest thing to do is use the package that has the isomorphic API, and which is the easiest to configure and use.  Go with `miktam:loggly`.

If, however, you want an isomorphic API, and want to follow the Node.js best practices, then you'll want to follow the `winston-loggly-bulk-demo`.


