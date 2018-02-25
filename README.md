# Chimp.js
[![Circle CI](https://circleci.com/gh/xolvio/chimp.svg?style=shield&circle-token=:circle-token)](https://circleci.com/gh/xolvio/chimp) [![npm Version](https://img.shields.io/npm/dm/chimp.svg)](https://www.npmjs.com/package/chimp) [![Gitter](https://img.shields.io/gitter/room/xolvio/chimp.svg)](https://gitter.im/xolvio/chimp) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com) [![License](https://img.shields.io/npm/l/chimp.svg)](https://www.npmjs.com/package/chimp) [![Slack Status](http://community.xolv.io/badge.svg)](http://community.xolv.io) 

###### An awesome *developer-centric* experience for writing GUI tests.

Chimp takes away all the pain associated with setting up libraries and tools, and allow you to focus on building-in quality.

![Chimp by Xolv.io](./images/logo.png?raw=true)

Whether you're writing UI unit tests, functional end-to-end tests, or user-emulating performance tests, Chimp will help you do it, faster.

Chimp allows you to:
* Write web automation tasks such as crawling, scraping, submitting forms and taking screenshots
* Write unit, integration, functional, acceptance, performance and end-to-end tests using the following frameworks:
  * [Mocha](https://mochajs.org)
  * [Jest](https://facebook.github.io/jest)
  * [Jasmine](https://jasmine.github.io)
  * [AVA](https://ava.li)
  * [Cucumber.js](https://github.com/cucumber/cucumber-js)  
  * [Cypress](https://cypress.io) 
* Use a common API across the following drivers:
  * [Enzyme](http://airbnb.io/enzyme)
  * [WebdriverIO](http://webdriver.io)
  * [Puppeteer](https://github.com/GoogleChrome/puppeteer)
  * [Cypress](https://cypress.io)
* Use a common API across the following browsers:
  * [JSDOM](https://github.com/tmpvar/jsdom) (emulated Node.js headless browser)
  * [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver) (real Chrome headless and windowed)
  * [Selenium](http://www.seleniumhq.org/docs/01_introducing_selenium.jsp) (real browsers like Chrome, Firefox, Safari, Internet Explorer and Opera)

## Getting started
### 1. Install Chimp in your project
```
npm i --save-dev chimp@next 
```
*Note: the `@next` tag will be removed once Chimp 2.0 is released.*

### 2. Import the Chimp driver
```javascript
import driver from 'chimp/driver'
```

### 3. Use the driver object with the Chimp API
```javascript
await driver.url('http://xolv.io')

const title = await driver.getTitle()

console.log(title)
```

That's it! No complex tools to configure, no painful test-ops to setup, just import and drive.  

See examples of Chimp in action:
* [Mocha](./test-project/mocha.spec.js)
* [Jest](./test-project/jest.spec.js)
* [Jasmine](./test-project/jasmine.spec.js)
* [AVA](./test-project/ava.spec.js)
* [Cucumber](./test-project/features)
* [Cypress]() TODO
* [Meteor]() TODO
* [Performance Test]() TODO
* [Web Automation]() TODO

### The Chimp Driver (formerly Browser)
Chimp provides a **common API** that can **interchangeably** be used across the following driver configurations:
- [ ] Enzyme
- [x] WebdriverIO + ChromeDriver 
- [ ] WebdriverIO + Selenium Standalone (local Firefox/IE/Safari)
- [ ] WebdriverIO + Selenium Grid (remote Firefox/IE/Safari - e.g. BrowserStack)
- [ ] Puppeteer
- [ ] Cypress

Wait.. what? Interchangeably?  

Yes! You can Chimp's common API and swap out the driver based on your needs.

You see we love to reuse code, and we have found that it saves a lot of time to write automation code that can be used for multiple purposes across testing boundaries, drivers browsers and frameworks. 


## The Chimp PageObject 

Chimp now contains a `PageObject` class that you can import and use in unit, integration, functional, acceptance, end-to-end and performance tests as well as any general automation tasks.

To see how this magic works, refer to the inline comments in the code below.

```javascript
import driver from 'chimp/driver'
import PageObject from 'chimp/page-object'

// 1. Extend the PageObject class provided Chimp
class LoginComponentPageObject extends PageObject {
  // 2. Implement the `selectors` method
  selectors() {
    const root = '.login'
    return {
      root,
      username: `${root}__username`,
      password: `${root}__password`,
      errorMessage: `${root}__message--error`,
      submit: `${root}__submit`,
    }
  }
  // 3. Access the UI elements using the same keys as your selectors to create high-order functions that match your domain
  async login({username, password}) {
    await this.username.type(username)
    await this.password.type(password)
    await this.submit.click()
  }
}

// 4. You bind this page object to your desired context like this:
const loginComponentPageObject = new LoginComponentPageObject()
loginComponentPageObject.bindTo(context) // see below for examples of context binding
```

Note that you can also use the PageObject.extend if you prefer not to use classes. [See here.]() TODO.


#### PageObject Reuse Example #1
* Framework: Mocha
* Driver: Enzyme
* Browser: JSDOM

```javascript
import LoginComponentPageObject from 'login-component-page-object'
import {shallow} from 'enzyme'
import td from 'testdouble' 

describe('Login Component', () => {
  describe('render', () => {
    before(async () => {
      const wrapper = shallow(<LoginComponent />)
      this.loginComponent = new LoginComponentPageObject(wrapper)   
    })
    it('should show the username field', () => {
      expect(this.loginComponent.username).to.be.visible()
    })
    it('should show the password field', () => {
      expect(this.loginComponent.password).to.be.visible()
    })
    it('should show the login button', () => {
      expect(this.loginComponent.username).to.be.visible()
    })
  })
  describe('submit', () => {
    // Use page objects where possible for reuse
    it('should show an error if the username field is empty', async () => {
      const loginComponent = new LoginComponentPageObject(shallow(<LoginComponent />))  
      
      await loginComponent.login({username: '', password: 'foo'})
      
      expect(loginComponent.username).to.be.visible()
    })
    // Feel free to also write unit tests without page objects
    it('should not make a data if the username field is empty', () => {
      const props = { request: td.function() }
      const wrapper = shallow(<LoginComponent {...props}/>)
      
      wrapper.find(CustomButton).click()
      
      td.verify(props.request(), {times: 0})
    })
  })
})
```

#### PageObject Reuse Example #2
* Framework: Cypress (Mocha)
* Driver: Cypress
* Browser: Chrome (windowed)

```javascript
// TODO
```

#### PageObject Reuse Example #3
* Framework: Jest
* Driver: WebdriverIO
* Browser: Chrome (headless)

```javascript
// TODO
```


#### PageObject Reuse Example #4
* Framework: Cucumber.js
* Driver: WebdriverIO
* Browser: Chrome (windowed)

```javascript
// TODO 
```

#### PageObject Reuse Example #5
* Framework: Jest
* Driver: Puppeteer
* Browser: Chrome (headless) on AWS Lambda

```javascript
// TODO 
```

#### PageObject Reuse Example #6
* Framework: None (generic automation)
* Driver: Puppeteer
* Browser: Chrome (headless)

```javascript
// TODO
```

For the full Chimp API, [see the API docs below](#chimp-api)

### Debugging
Need to see what the driver is doing? No problem, just append `.debug` to your driver import like this:

```javascript
import driver from 'chimp/driver.debug'
```

Chimp will give you a non-headless driver so you can do your job. This is very powerful when it's used with [Webdriver.io's REPL debug method](http://webdriver.io/api/utility/debug.html) like this:

TODO how does this work with Enzyme and Puppeteer?

```javascript
import driver from 'chimp/driver.debug'

await driver.debug() // you can now use the console REPL interface
```

Note: Be sure to set the test timeout value of your test framework to be high so it does not timeout and stop the test while you are debugging. Here's how you do this in the different testing frameworks:

```bash
mocha --compilers js:babel-core/register my-spec.js --watch --timeout 100000
jest TODO
jasmine TODO
ava TODO
cucumber TODO
```

### Watching
Since Chimp will work with any file watcher. You can use [Gulp.js](https://gulpjs.com), [Grunt](https://gruntjs.com), [Karma](https://karma-runner.github.io/2.0/index.html), a custom watcher that you've built, or some test frameworks that have a watch mode, like Mocha's CLI for example:
```bash
mocha --compilers js:babel-core/register my-spec.js --watch
```

Chimp does not care who or what is doing the watching, so long as Chimp is running in a long-running process, it will keep a dedicated driver running to be used for automation.

### Using Meteor?
Chimp has evolved from a Meteor project and we will always provide 1st class support for Meteor. You can get access to a DDP connection by doing the following:

```javascript
import server from 'chimp/meteor/server'
``` 

Then you can use this `server` object to make DDP calls like this:

```javascript
const result = await server.call('your-meteor-method')
```
#### Executing remote code
> *NOTE: This feature is still in development and not yet ready. We're starting with the README to drive out the requirements*

And if you install the `xolvio/backdoor` Meteor package, you can also run code directly on the server like this:
```javascript
// write a function you'd like to run on the server
function getMeteorSettings(setting) {
  return Meteor.settings[setting]
}

// use server.execute to call the function within the Meteor context with parameters if needed
const mongoUrl = server.execute(getMeteorSettings, 'mongoUrl')

// returned values come back from the function that ran in the Meteor context
console.log(mongoUrl)
```

### Configuring Chimp
> *NOTE: This feature is still in development and not yet ready. We're starting with the README to drive out the requirements*

To configure Chimp, its drivers, and the tools & libraries that it uses, you have a few options available to you that are listed below.

#### Create a `chimp.config.js` file
This can be anywhere up the directory tree. The file looks something like this: 
```javascript
export default {
  driver: {
    // see Configuration options
  },
  meteor: {
    // see Configuration options
  }
}
```

#### Use the `package.json` 
This can be anywhere up the directory tree. The file looks something like this:
```javascript
{
  driver: {
    // see Configuration options
  },
  meteor: {
    // see Configuration options
  }
}
```


#### Create a `chimprc` file 
This can be anywhere up the directory tree. The file looks something like this:
```javascript
{
  driver: {
    // see Configuration options
  },
  meteor: {
    // see Configuration options
  }
}
```

#### Configuration options
> *NOTE: This feature is still in development and not yet ready. We're starting with the README to drive out the requirements*

##### driver

Chimp has support for the drivers listed below. Click on each entry to see the available configuration options:
* [`webdriverio`](#webdriverio)
* [`puppeteer`](#puppeteer)


###### webdriverio
When you use this driver, Chimp starts a [chromedriver](https://sites.google.com/a/chromium.org/chromedriver) instance for you by default. If you would like to use other drivers like `firefox`, `safari` and `msie`, then you need to tell Webdriver.IO to set the `desiredCapabilities` in the configuration. For example, if you wanted to use `firefox`, you do this:
```javascript
{
  driver: {
    name: 'webdriverio',
    options: {
      desiredCapabilities: {
        browserName: 'firefox'
      }
    }
  }
}
```
Chimp passes any configuration you place in under the `options` object directly to Webdriver.io. [Refer to the Webdriver.io configuration documentation](http://webdriver.io/guide/getstarted/configuration.html) for details.

When using a browser other than Chrome with WebdriverIO, Chimp will automatically download and start Xvfb and Selenium so you can still have headless magic. Note that for this to work, you will need:
* An OS that has an X windows manager (will not work on OSX) 
* Compatible version of Java to run Selenium

###### puppeteer
TODO

##### meteor
TODO

## Chimp API

#### addCommand({name, implementations})
 
```javascript
/**
 * Allows you to add custom commands along with implementations for all the supported Chimp drivers.  
 *
 * @param {string} name - The name of this method. This will be added to the `driver` object
 * @param {Object} implementations<driver, implementation> - A set of driver implementations
 * @param {string} implementations.driver - The driver to implement the command for. You can import the chimp/drivers to get a list of available drivers, e.g. drivers.PUPPETEER or drivers.WEBDRIVERIO
 * @param {string} implementations.implementation - The method that implements
 * 
 * @return {undefined} 
 */
```

#### getDriver()

```javascript
/**
 * Provides access to the underlying driver being used by Chimp.
 * 
 * CAUTION: Be mindful that any code that uses the driver directly will lock you to using that driver. You might want to consider the [`addCommand`](#addCommand) method that allows you add multiple implementations.
 * 
 * If you find a command that you use that is not supported, please get in touch to let us know, or even better submit a Pull Request. You can see the guidelines for [adding PageObject API command here]() TODO
 * 
 * Below you can find links to the driver APIs used by Chimp: 
 *   [WebdriverIO]() TODO
 *   [Puppeteer]() TODO
 * 
 * @return {object}  - The driver being used in the current context
 */
```

## How Chimp works
What is this trickery you might ask yourself? How is Chimp providing a ready to use browser simply with an `import`/`require`?

TODO

---

## Chimp Developers Guide:
We've made it easy for you to get ready to submit PR's and develop Chimp features. To get up and running, follow these steps:

We develop on the latest versions of everything (Node, NPM and packages), and we use tests to ensure backwards compatibility on CI.

1. Make sure you're on the latest version of Node and NPM. We recommend using [NVM](https://github.com/creationix/nvm).
2. `git clone git@github.com:xolvio/chimp.git` 
3. `npm run setup` - [See below](#npm-run-setup`) 
4. `npm run watch` - [See below](#npm-run-watch`)

You can now make changes to the source files in the Chimp directory and use the `./test-project` directory to use features within testing frameworks. 

Within the `test-project` directory, you can run the following npm scripts:
```bash
# run individual test frameworks
npm run mocha
npm run jest
npm run jasmine
npm run cucumber
npm run ava
```

### Available NPM Scripts
The following scripts are available to you as a developer to help you with submitting new features and PR's. See below for a description of each:

###### `npm run clean`
TODO 

###### `npm run transpile`
TODO 

###### `npm run watch`
TODO 

###### `npm run test`
TODO 

###### `npm run prebpublish`
TODO 

###### `npm run install`
TODO 

###### `npm run setup`
TODO 

###### `npm run reset`
TODO 

### Debugging
Process management can be very tedious. Here's a script that can help you see the processes being started/stopped by Chimp
```bash
while true; do clear; ps -ef | grep chrome; sleep 1; done
``` 

### Releasing
1. Increase the version in the `chimp/package.json` file
2. `npm run publish`
