nightwatch-commands
===================

[![NPM version](https://badge.fury.io/js/nightwatch-commands.svg)](http://badge.fury.io/js/nightwatch-commands)

A set of Mobify specific custom commands for Nightwatch.js

### Download
To begin, clone this repository. `cd` into your chosen folder, and run the following:
    `npm install`

Selenium server and Chromedriver will also be downloaded into `/selenium`. 

### Linting
JavaScript in this tool is linted with [ESLint](http://eslint.org/) according to our code [syntax and style standards](https://github.com/mobify/mobify-code-style) here at Mobify.

Linting may be run with the `grunt lint` command. Code is also linted automatically on [CircleCI](https://circleci.com/).

### Use
In order for your project to be able to access these commands and assertions you need to include them in your projects nightwatch.js `settings.json` file.

It should look something like this:

```
"custom_commands_path": "./node_modules/nightwatch-commands/commands",
"custom_assertions_path": "./node_modules/nightwatch-commands/assertions"
```

## Assertions

#### elementsCount(selector, expected, message, callback)

The `elementsCount` assertion checks if the given selector is present the number of times that is expected for that selector to appear.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
selector       | String         | The CSS/Xpath selector to locate the element.
expected       | Number         | The expected number of times for the attribute to appear.
message        | String         | _optional_ The message to output.
callback       | Function       | _optional_ A function to call after the current command finishes execution.

```
this.demoTest = function (browser) {
    browser.assert.elementsCount('#x-root', 1);
}
```

#### elementsPresent(selector, message, callback)

The `elementsPresent` assertion checks if the given selectors are present. It returns a list of the missing selectors.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
selector       | String         | The CSS/Xpath selector to locate the element.
message        | String         | _optional_ The message to output.
callback       | Function       | _optional_ A function to call after the current command finishes execution.

```
this.demoTest = function (browser) {
    browser.assert.elementsPresent('#x-root', '#x-header', '#x-footer');
};
```

#### elementsVisible(selector, message, callback)

The `elementsVisible` assertion checks if one or more selectors are visible. It returns a list of one or more selectors that are not visible on the page.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
selectors      | String         | The CSS/Xpath selector to locate the element.
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

```
this.demoTest = function (browser) {
    browser.assert.elementsVisible('#x-root', '#x-head');
};
```

#### templateName(expected, message, callback)

The `templateName` assertion checks if the given template name is correct.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
expected       | String         | The expected value of the attribute to check.
message        | String         | _optional_ A log message to display in the output. If the parameter is not specified, a default message is displayed.
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

```
this.demoTest = function (client) {
    browser.assert.templateName('home');
};
```

## Commands

#### get(url, callback)

The `get` command combines the `url` and `waitUntilMobified` functions. This command uses the `browser.execute` function to run code within the client browser. It then waits for the `Mobify`or `Adaptive` object to be present on the page

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
url            | String         | The URL to load
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

```
this.demoTest = function (browser) {
    browser.get('http://www.test.com');
};
```

#### getMobifyEvaluatedData(callback)

The `getMobifyEvaluatedData` command uses the `waitForCondition` method to retrieve the `Mobify.evaluatedData` from the client browser.

Input

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

Output

Parameter Name | Description
-------------- | -----------
Object         | Returns the client object after `waitUntilMobified` executes on it with the specified parameters.

```
this.demoTest = function (browser) {
    browser.getMobifyEvaluatedData();
};
```

#### htmlCapture(message, callback)

`htmlCapture` is a helper command that saves HTML to `tests/system/integration/fixtures`. This command was designed to collect and save HTML files for use in integration tests. This command is intended to be run using a Nightwatch environment with a desktop user agent, however, it can still be used to capture any HTML as required.  

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
fileName       | String         | camelCased alphanumeric string ending with ".html", representing the name of the fixture
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

```
this.demoTest = function (browser) {
    browser.htmlCapture('cart.html');
};
```

#### log(message, callback)

The `log` command prints a message to the console. Use this command to output messages in a test.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
message        | String         | The message to log on the console.
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

```
this.demoTest = function (browser) {
    browser.log('Testing submitting form');
};
```

#### browse(selector, callback)

The `browse` command initiates a `click` command on the supplied selector link, browses to the URL, and then it initiates the `waitUntilMobified` function before it continues the chain of tests.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
selector       | String         | The CSS selector to click on to browse to the new URL.
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

```
this.demoTest = function (browser) {
    browser.browse('.myLink');
};
```

#### preview(url, callback)

The `preview` command uses http://preview.mobify.com to open a website to preview a given bundle. The bundle and the base URL need to be set in the `tests/system/site.json`, `tests/system/site.js` or `system/site.js` file. Note that if the "production" flag is set in the `activeProfile` in `site.json` or `site.js`, the bundle URL will be ignored. Pass in an optional URL as an argument to this command. Upon completion, `waitUntilMobified` is called to ensure that the mobile site adaptation is complete.

Example site.json
```
{
    "activeProfile": "production",
    "profiles": {
        "default": {
            "bundleUrl": "http://localhost:8080/adaptive.js",
            "siteUrl": "http://www.merlinspotions.com/"
        },
        "production": {
            "bundleUrl": "",
            "siteUrl": "http://www.merlinspotions.com/",
            "production": true
        }
    }
}
```

Example site.js
```
var Site = {
    /*
     activeProfile defines which environment to run tests against.
     By default, builds on master branch run against production, without preview.
     Builds on any other branch should use preview with local adaptive.js.

     Change activeProfile whenever you need to override the default behaviour.
    */
    activeProfile: process.env.ACTIVE_PROFILE || 'default',

    /*
     Define new profiles as needed for different URLs, eg. staging, prod.
    */
    profiles: {
        default: {
            bundleUrl: 'http://localhost:8080/adaptive.js',
            siteUrl: 'http://www.merlinspotions.com/'
        },
        production: {
            bundleUrl: '',
            siteUrl: 'http://www.merlinspotions.com',
            production: true
        }
    }
};

module.exports = Site;

```

If the project does not have a `site.json` or `site.js` file, this command is equivalent to the `url` protocol command.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
url            | String         | _optional_ The URL to preview.
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

```
this.demoTest = function (browser) {
    browser.preview();
};

this.demoTest = function (browser) {
    browser.preview('http://my-awesome-project.com');
};
```

#### trigger(selector, type, callback)

The `trigger` command simulates a specified event type on the supplied DOM element specified by the selector parameter.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
selector       | String         | The CSS/Xpath selector to locate the element.
type           | String         | The specified event type, for example `click` in the enabled JSON Wire Protocols.
callback       | Function       | _optional_ A function to call after the current command finishes execution.  

```
this.demoTest = function (browser) {
    browser.trigger('.myLink', 'click');
};
```

#### triggerTouch(selector, type, callback)

The `triggerTouch` command simulates a specified touch type event on the supplied DOM element. Use this command when Selenium's `click` does not register.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
selector       | String         | The CSS/Xpath selector to locate the element.
type           | String         | The specified event type, for example `click` in the enabled JSON Wire Protocols.
callback       | Function       | _optional_ A function to call after the current command finishes execution.   

```
this.demoTest = function (browser) {
    browser.triggerTouch('.myLink', 'click');
};
```

#### waitForAjaxCompleted(callback)

The `waitForAjaxCompleted` command uses the `waitForCondition` function to execute code within the client browser. The command checks the value of `jQuery.active` to ensure that the number of active connections is 0.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
callback       | Function       | _optional_ A function to call after the current command finishes execution.   

```
this.demoTest = function (browser) {
    browser.waitForAjaxCompleted();
};
```

#### waitForAnimation(milliSeconds, callback)

The `waitForAnimation` command suspends the test for the given time in milliseconds while it waits for animation to complete.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
milliSeconds   | Number         | The number of millliseconds to wait.
callback       | Function       | _optional_ A function to call after the current command finishes execution.   

```
this.demoTest = function (browser) {
    browser.waitForAnimation();
};
```

#### waitForCondition(condition, milliSeconds, timeout, message, callback)

The `waitForCondition` command receives a condition to check for, waits for a maximum time before timing out, and polls at a specified time interval. The condition returns either as a success or a timeout.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
condition      | Function       | The condition to check against.
milliSeconds   | Number         | _optional_ The number of milliseconds to poll before timeout.
timeout	       | Number         | _optional_ The number of milliseconds between each poll.
message        | String         | _optional_ The message to output.
callback       | Function       | _optional_ A function to call after the current command finishes execution.   

```
this.demoTest = function (browser) {
    return browser.waitForCondition('return $.active;', 8000, function(result) {
        if (typeof callback === 'function') {
           callback.call(browser, result);
        }
    });
};
```

#### waitForContextsReady(expectedContextsCount, milliseconds, timeout, messages, callback)

The `waitForContextsReady` command receives a number of expected contexts to check for, waits for a maximum time before timing out, and polls at a specified time interval. Used with Appium when testing hybrid mobile web apps. See http://nightwatchjs.org/api/contexts.html

Parameter Name          | Parameter Type | Description
-------------           | -------------- | -----------
expectedContextsCount   | Number         | The condition to check against.
milliSeconds            | Number         | _optional_ The number of milliseconds to poll before timeout.
timeout                 | Number         | _optional_ The number of milliseconds between each poll.
message                 | String         | _optional_ The message to output.
callback                | Function       | _optional_ A function to call after the current command finishes execution.   

```
this.demoTest = function (browser) {
    browser.waitForContextsReady(3, 8000);
    // At this point, it is safe to switch the context to either the NATIVE
    // context or any of the WEBVIEW_* contexts.
};
```

#### waitForUrl(url, milliseconds, timeout, messages, callback)

The `waitForUrl` command waits until the page URL is equal to the specified `url`.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
url            | String         | Expected URL
milliSeconds   | Number         | _optional_ The number of milliseconds to poll before timeout.
timeout        | Number         | _optional_ The number of milliseconds between each poll.
message        | String         | _optional_ The message to output.
callback       | Function       | _optional_ A function to call after the current command finishes execution.   

```
this.demoTest = function (browser) {
    browser.waitForUrl('http://www.google.ca/');
};
```

#### waitForUrlToContain(url, milliseconds, timeout, messages, callback)

The `waitForUrlToContain` command waits until the page URL contains the specified `url`.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
url            | String         | A partial URL to match against.
milliSeconds   | Number         | _optional_ The number of milliseconds to poll before timeout.
timeout        | Number         | _optional_ The number of milliseconds between each poll.
message        | String         | _optional_ The message to output.
callback       | Function       | _optional_ A function to call after the current command finishes execution.   

```
this.demoTest = function (browser) {
    browser.waitForUrlToContain('google');
};
```

#### waitUntilMobified(milliSeconds, callback)

The `waitUntilMobified` command will use the `waitForCondition` command to poll for the Mobify or Adaptive object on the page to ensure that the adaptation is complete. Use this command to browse to a page or if the page reloads.

Parameter Name | Parameter Type | Description
-------------  | -------------- | -----------
milliSeconds   | Number         | _optional_ The number of milliseconds to poll before timeout.
If not specified, the default timeout is 10,000 milliseconds.
callback       | Function       | _optional_ A function to call after the current command finishes execution.   

```
this.demoTest = function (browser) {
    browser.waitUntilMobified();
};
```
