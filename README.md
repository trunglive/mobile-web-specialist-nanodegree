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


