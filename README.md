# ifihadinvestedincrypto
__________________________________________
A Node.JS Application That Helps People Understand Their Investment Shortcomings and Feel Bad About It. </br>
__________________________________________

**School Year (Mine & Michael's):** Freshman @ University of Maryland (class of 2021) </br>

## Site Will Be Live At : http://www.ifihadinvestedincrypto.com/
<b>Note:</b> The site will take about 5-10 seconds to load since it is running on a free Heroku Dyno. A Heroku Dyno basically follows a ServiceBus-Worker architecture where requests will be recieved by a client facing Web Role that directs them to a ServiceBus which holds a Queue of requests to be processed. The Worker Role will then poll the ServiceBus queue and have the business logic to process requests and do whatever (persist data to db, fetch stuff, etc.). This design allows for efficient scaling of operations. Have more clients accessing your api/site/service? Add more web roles. A ton of requests? Expand the Servicebus queue (or deploy more service buses I think?). Need more processing power? Deploy more workers. The web and worker roles are what make an application up so "application" is a blanket term for a system of multiple cooperating parts.

When you have a free Heroku plan, the web dynos sleep leading to bad loading time on first request since the dyno has to "wake-up". After a period of inactivity it will sleep again. This is why the loading sucks. [More On Dynos Here](https://www.heroku.com/dynos)
#### Site Is Built From Scratch.
#### This is a public repo just to display a journal up to launch.

**Project Start Date:** 1/14/18 </br>
**v1.0.0 Published:** 1/21/18 </br>
**v1.1.0 Published:** TBD </br>

#### The Problem:
You know those people that always see a stock or asset skyrocket in value and then say, "Yeah...I called it. IF I HAD INVESTED IN *insert asset* I WOULD BE RICH!". With crypto currencies seeing a huge explosion in popularity more and more often do we hear out close friends or even family say that about the infamous [Bitcoin](https://www.investopedia.com/articles/investing/123015/if-you-had-purchased-100-bitcoins-2011.asp). I thought all of this was pretty funny and later this idea came to me from nowhere while I was taking a shower.

### The Solution:
This site. Allow people to visually understand their investment shortcomings from all angles and make them feel bad. (of course I'm joking...am I?)

### The Initial Idea:
At first I'll make a simple single page site and make it all fancy and mobile responsive. It will do basic calculation on what you would have gained or lost on a crypto currency from the start date to the present date of doing the calculation. From there I hope to expand the site to have more sections that are funny where you can see what you could've bought and the amount of happiness you missed out on. Hopefully I can get there.

# Launch Journal:
## Day One Pt. 1 (Non-Responsive Webpage I Built To Get The Vision Out)
<a href="http://ibb.co/kyejSR"><img src="http://preview.ibb.co/n169u6/Screen_Shot_2018_01_14_at_10_56_23_AM.png" alt="Screen_Shot_2018_01_14_at_10_56_23_AM" border="0"></a> </br>

### Day One Pt. 2
[Michael](https://github.com/mwein99) added to the team. </br> </br>

### Day One Pt. 3
Michael Made The Site Look A Lot Nicer </br>
<a href="http://ibb.co/i1MGsR"><img src="http://preview.ibb.co/gDLnz6/Screen_Shot_2018_01_15_at_1_00_06_AM.png" alt="Screen_Shot_2018_01_15_at_1_00_06_AM" border="0"></a> </br> </br>

## How It Works: </br>
So when the idea first came to mind I knew that I just wanted to make a minimalist 1 page application for this (the drawer feature with "What You Could've Bought" was later thought of later). I knew that working with currency and dates would be annoying but knew it'd be cool to support a ton of currencies just for the heck of it and dates. Michael was a crucial part in helping refine the logic of the application and in imporving the UX. I'll be looking thorugh the code as I write this "how it works" so it'll be sort of like a conversation. </br>

### Server Side </br>
Um...not much serverside processing happens for this application. Michael and I knew page reloads (like what is caused by a call to the server and a redirect) would be a hitch to a pretty and smooth UX so all the currency logic, api calls encapsulated in promises, etc. are clientside served in the app.js file. I personally saw that there wouldn't be anything too rigorous that would require talking to the server and no data needs to be persisted so...yeah...all the more for having all operations on the client as of now. </br>

We have the app.js file where imported middleware is initialized like storing session data, setting up static asset pointers, and attatching the separate route file (index.js) to the base uri '/' which is the root. app.js finishes out with an error handler to catch 404's and other errors. Finally, the fully configured app variable is passed to bin/www.js where the server is actually started taking into account the environment (if process.env.PORT is defined then it is started on that port. If not then the default is port 3000). </br>

Like I said, there is a separate index.js file for routing requests. There are only two routes. A route for '/' and a route for '/contact'. Nothin' too crazy. At the end of index.js the router is exported to be consumed by app.js (which like I said before pass a fully configured app to bin/www.js for starting the application). </br>

### Client Side </br>
This is where the magic happens. So let me walk down the app.js file that the client receives and just discuss things: </br>

#### Global Variables </br>
So first we define global variables for what we will need for the calculation. These variables are initialized with what the user sees as placeholders and everytime an input field is updated these global variables are updated as well (the data is extracted from the DOM) so they can be used whenever a user wants to perform the calculation.

#### Helper Functions </br>
I'll talk about helper functions here since I know they get hoisted (that word makes me sound smart) at compile time although we put them at the bottom of the file. We have helpers for: </br>
- Checking everytime an input field was updated whether the company had existed at that time and warning the user of a possibly impossible calculation
- Converting a month, day, and year to epoch time for time comparison and api calls
- Formatting any raw number with commas appropriately
- Purging a number of commas
- A simple currency conversion function that takes an amount and the conversion rate
- A function for calculation investment profit given start investment, # of shares purchased, and the ending value of the asset
- A custom animation function that takes a number to count up to and the jQuery object target of the animation. It dynamically adjusts to the number's size so the countUpTo animation is the perfect length

#### Promise Factory Functions </br>
Then we have some functions that serve up helpful promises that avoid tight coupling in the onclick code and of course allow us to wait for certain operations to happen before we proceed with a certain calculation etc. These functions return promises to be executed later and can be configured by what you pass into the function. We have:
- A function that returns a promise for a fetch of a crypto's value today in a currency of choice
- A function that resolves a promise for of how many shares you could've bought with the input amount and currency type. It pass the numShares bought, foreignExchangeRate of the currency to USD for later calculations if it isn't in USD, and whether the result isZero (a boolean). The next promise in execution will consume this information.
- Finally there is a convert currency function that returns a promise that will return an amount converted from a curreny to an end currency using currencyconverterapi.com
<b>- These all combine to form the core functionality onclick plus some side logic</b>

#### Utility Objects </br>
We then have JS objects that contain companies start dates etc. For input validation (to prevent a user trying to calculate how much they would've made investing in a company before it went public). There is also the JS object of the potentialItems that populate the drawer after a price is calculated and a later helper function spits out some items that could've been bought as well as what would've been leftover.

#### Fetch On Page Load </br>
So we know that daily bitcoin's value changes. It is the default currency we have set for the site on page load. So what we do is just leave $X,XXX,XXX in the field and on page load a get request is made to the cryptocompare.com api (where we get our crypto price data) and we do all of our calculations immediately then use a formatting helper function before we inject the final result into the value field.

#### Input Box Change Listeners </br>
Then we have all of our listeners that update the global variables

## Challenges Encountered </br>
1.) Working with 25+ foreign currencies and dates is a pain in the 🐴. Nuff said. </br>
</br>
2.) Async JS and Promises and requests flying everywhere. Nuff nuff said. </br>
</br>

## Built With
* [Node.js](https://nodejs.org/en/) - Event-Driver Server-side Platform used
* [Express.js](https://expressjs.com/) - Node.js web application framework used
* [Bootstrap](https://getbootstrap.com/) - Bootstrap for Frontend
* [jQuery](https://jquery.com/) - Client-Side Document Object Model (DOM) Manipulations and Asynchronous JavaScript & XML (AJAX) Requests
* [Gulp](https://gulpjs.com/) - Development Workflow Task Automation
* [npm](https://www.npmjs.com/) - Package Manager
* [Atom](https://atom.io/) - Text Editor

## The Team
* [Me](https://github.com/bephrem1/) - Backend Developer (Some Frontend)
* [Michael Weinberger](https://github.com/mwein99) - Frontend Developer

## Credits
* [Flaticon](https://www.flaticon.com/) - Icon assets
