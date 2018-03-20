## :sparkles::sparkles: My notes on the course as I progress :sparkles::sparkles:
> The content of the notes is mostly from the quizzes of the course.

### Lesson 2: The Benefits of Offline First
* 2.4 The reasons why users could not receive data from websites:
  * poor signal
  * misconfigured proxy
  * fault in mobile network
  * busy network
  * server being DDOSed
  * bug in server code
  * wifi captive portal
  * moon's gravitational pull
* 2.5 The conditions in which the apps work well:
  * good connectivity
  * offline
* 2.6 Examples of Offline-first techniques
  * deliver the page's header & content from a cache, then fetch the updated content from the network
  * deliver the page's header from a cache, then fetch the content from the network
* 2.8 & 2.9 Clone this repo & run on local machine to start working on the Wittr App
   ```
   git clone https://github.com/jakearchibald/wittr
   npm install
   npm run serve
   ```
   then open ```http://localhost:8888``` to see the site & ```http://localhost:8889``` to adjust the setting
* 2.11 Change connection types
  * open ```http://localhost:8889```, try switching the setting and put **demo** in Test ID
* 2.12 Testing Lie Fi mode
  * open ```http://localhost:8889```, try Lie-fi and put **lie-fi** in Test ID
   
### Lesson 3: Introducing the Service Worker
3.2 Scoping - given the registration code below:
   ```javascript
   navigator.serviceWorker.register('/sw.js', {scope: '/foo/'});
   ```
   The page URLs that the service worker control are:
   ```
   /foo/
   /foo/bar
   /foo/bar/index.html
   ```
3.4 Register a Service Worker
   ```
   git reset --hard
   git checkout task-register-sw
   ```
   - The service worker script is located at ```/public/js/sw/index.js```
   - Register a service worker at ```/public/js/main/IndexController.js```
   
   ```javascript
   IndexController.prototype._registerServiceWorker = function() {
     // older browsers that do not support service worker
     if (!navigator.serviceWorker) return;
     
     // browsers that support service worker
     navigator.serviceWorker.register('/sw.js').then(function() {
       console.log('Registration worked!');
     }).catch(function() {
       console.log('Registration failed!');
     });
   }
   ```
3.11 Hijacking Requests 1
   ```
   git reset --hard
   git checkout task-custom-response
   ```
   Change the service worker's response in ```/public/js/sw/index.js```
   ```javascript
   self.addEventListener('fetch', function(event) {
   // respond to all requests with an html response
   // containing an element with class="a-winner-is-me".

  event.respondWith(
    new Response('<p class="a-winner-is-me">Awesome!!</p>', {
      headers: {
        'Content-Type': 'text/html'
      }
    })
  );
  console.log(event.request);
  });
  ```
3.13 Hijacking Requests 2
  ```
  git reset --hard
  git checkout gif-response
  ```
  Open ```/public/js/sw/index.js``` and intercept all requests ending with **.jpg** by an evil image
  ```javascript
  self.addEventListener('fetch', function(event) {
    if (event.request.url.endsWith('.jpg')) {
      event.respondWith(fetch('/imgs/dr-evil.gif'));
    }
  });
  ```
3.15 Hijacking Requests 3
  ```
  git reset --hard
  git checkout error-handling
  ```
  Return dr-evil gif image for 404 errors
  ```javascript
  self.addEventListener('fetch', function(event) {
    event.respondWith(
      fetch(event.request).then(function(response) {
        if (response.status === 404) {
          return fetch('/imgs/dr-evil.gif');
        }
        return response;
      }).catch(function() {
        return new Response("Uh oh, that totally failed!");
      })
    );
  });
  ```
3.17 Install and Cache
  ```
  git reset --hard
  git checkout task-install
  ```
  Add cache from urlsToCache
  ```javascript
  self.addEventListener('install', function(event) {
    var urlsToCache = [
      '/',
      'js/main.js',
      'css/main.css',
      'imgs/icon.png',
      'https://fonts.gstatic.com/s/roboto/v15/2UX7WLTfW3W8TclTUvlFyQ.woff',
      'https://fonts.gstatic.com/s/roboto/v15/d-6IYplOFocCacKzxwXSOD8E0i7KZn-EPnyo3HZu7kw.woff'
    ];

    event.waitUntil(
      caches.open('wittr-static-v1').then(function(cache) {
        return cache.addAll(urlsToCache);
      })
    );
  });
  ```
  
3.18 Cache Response
  ```
  git reset --hard
  git checkout task-cache-response
  ```
  Respond with an entry from the cache if there is one. If there isn't, fetch from the network.
  ```javascript
  self.addEventListener('fetch', function(event) {
    event.respondWith(
      caches.match(event.request).then(function(response) {
        if (response) return response;
        return fetch(event.request);
      })
    );
  });
  ```
  Remove old caches in scalable way
  ```javascript
  self.addEventListener('activate', function(event) {
    event.waitUntil(
      // TODO: remove the old cache
      caches.keys().then(function(cacheNames) {
        return Promise.all(
          cacheNames
            .filter(function(cacheName) {
              return (
                cacheName.startsWith('wittr-') && cacheName !== staticCacheName
              );
            })
            .map(function(cacheName) {
              return caches.delete(cacheName);
            })
        );
      })
    );
  });
  ```
3.23 Adding UX
  ```
  git reset --hard
  git checkout task-update-notify
  ```
  ```javascript
  IndexController.prototype._registerServiceWorker = function() {
    if (!navigator.serviceWorker) return;

    var indexController = this;

    navigator.serviceWorker.register('/sw.js').then(function(reg) {
      // If there's no controller, this page wasn't loaded
      // via a service worker, so they're looking at the latest version.
      // In that case, exit early
      if (!navigator.serviceWorker.controller) return;
      // If there's an updated worker already waiting, call
      // indexController._updateReady()
      if (reg.waiting) {
        indexController._updateReady();
        return;
      }
      // If there's an updated worker installing, track its
      // progress. If it becomes "installed", call
      // indexController._updateReady()
      if (reg.installing) {
        indexController._trackInstalling(reg.installing);
        return;
      }
      // Otherwise, listen for new installing workers arriving.
      // If one arrives, track its progress.
      // If it becomes "installed", call
      // indexController._updateReady()
      reg.addEventListener('updatefound', function() {
        indexController._trackInstalling(reg.installing);
      });
    });
  };

  IndexController.prototype._trackInstalling = function(worker) {
    var indexController = this;
    worker.addEventListener('statechange', function() {
      if (worker.state == 'installed') {
        indexController._updateReady();
      }
    });
  };
  ```
3.25 Triggering an Update
  ```
  git reset --hard
  git checkout task-update-reload
  ```
  From ```/public/js/main/IndexController.js```
  ```javascript
  // Line 41
  navigator.serviceWorker.addEventListener('controllerchange', function() {
    window.location.reload();
  });
  ```
  ```javascript
  // Line 57
  toast.answer.then(function(answer) {
    if (answer != 'refresh') return;
     // Tell the service worker to skipWaiting
    worker.postMessage({action: 'skipWaiting'});
  });
  ```
  From ```/public/js/sw/index.js```
  ```javascript
  // Line 46
  self.addEventListener('message', function(event) {
    if (event.data.action == 'skipWaiting') {
      self.skipWaiting();
    }
  });
  ```
3.26 Caching the Page Skeleton
  ```
  git reset --hard
  git checkout task-page-skeleton
  ```
  From ```/public/js/sw/index.js```
  ```javascript
  // Cache the skeleton instead of the root page
  // Line 38
  var requestUrl = new URL(event.request.url);

  if (requestUrl.origin === location.origin) {
    if (requestUrl.pathname === '/') {
      event.respondWith(caches.match('/skeleton'));
      return;
    }
  }
  ```
  
  ### Lesson 4: IndexedDB and Caching
  4.4 More IDB
  
  
  
