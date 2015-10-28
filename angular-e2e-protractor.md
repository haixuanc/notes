## Behind the Scenes: ElementFinders versus WebElements

When you call `driver.findElement`(locator), WebDriver immediately sends a command over to the browser asking it to locate the element. This isn't great for creating page objects, because we want to be able to do things in setup (before a page may have been loaded) like

```js
var myButton = ??;
```

and re-use the variable myButton throughout your test. ElementFinders get around this by simply storing the locator information until an action is called.

```js
var myButton = element(locator);
// No command has been sent to the browser yet.
```

The browser will not receive any commands until you call an action.

```js
myButton.click();
// Now two commands are sent to the browser - find the element, and then click it
```

- ElementFinders also enable chaining to find subelements, such as `element(locator1).element(locator2)`.
- Any action available in WebDriverJS on a WebElement is available on an ElementFinder.
- All WebElement actions are wrapped in this way and available on the ElementFinder, in addition to a couple helper methods like `isPresent`.
- You can always access the underlying WebElement using `element(locator).getWebElement()`, but you should not need to.

## References

- [Locators and ElementFinders](http://www.protractortest.org/#/locators)