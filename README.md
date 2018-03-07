## My notes on the course as I progress:
> The content of the notes is mostly from the quizzes I finished.

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
* 2.6 Offline-first techniques
  * deliver the page's header & content from a cache, then fetch the updated content from the network
  * deliver the page's header from a cache, then fetch the content from the network
* 2.8 & 2.9 Clone this repo & run on local machine to start working on the Wittr App
   ```
   git clone https://github.com/jakearchibald/wittr
   npm install
   npm run serve
   ```
  * open http://localhost:8888 for the website
  * open http://localhost:8889 to adjust the setting
   
   
* 2.11 Change connection types
   * open http://localhost:8889, try switching the setting and put **demo** in Test ID
* 2.12 Testing Lie Fi mode
   * open http://localhost:8889, try Lie-fi and put **lie-fi** in Test ID
