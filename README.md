# Test Locator Strategy

When writing automated UI tests with i.e. Playwright, Cypress or Selenium one of the first challenges to be addressed is: what locator strategy and format to use.

This project aims to help you to tackle this challenge and define a locator strategy which can be used universally in most of the user interfaces you want to automatically test.
Since in a modern agile way of software development, developers who create user interfaces are also responsible to write corresponding tests for their code, this strategy comes from the end of integrating speaking locators which can then easiely be reused in the test codes. Other projects or posts only address the challange from the side of a tester who needs to find locators in order to write test while other developers create the templates which are to be tested.

## General considerations
Elements which you want to refer to in automated tests always need to be identifiable by a static and therefore not randomly changing identifier. Trying to identify random locators is nearly impossible. We do not believe in self healing tests. When writing tests you must be able to make proper assumptions. If you cannot rely on elements to be identified by proper non-dynamic identifiers it can only be a good guess that you will find the right elements in self healing test, it cannot be a guarantee.

XPath as locators fall into the same category as non-static locator. If elements are moved into a different position in the DOM, likely the defined XPath locator breaks. Therefore it is not recommended to use those locators in test cases. As mentioned above go with static selector definitions. How that can be achieved is described below.

## Which attributes to use for a test locator
The best strategy to pick an HTML-attribute to use as a locator-holder is to use id or class names. These attributes are common element attributes which are valid on every known HTML element. In addition most of the modern test tools and libraries come with CSS-selectors which include means to easiely find ids or classes.

Whenever possible elements should get a unique identifier - use the attribute id in that case. A perfect example for such a unique element is a login button which only appears one single time in your application. Though it does not make sense to try to artificially make your elements unique. In case there is a search result which can vary in its returned search result rows over time, it is not advised to go with identifiers like result-item-{index}. However if you have access to some unique identifier for each and every result item a valid identifier could look like resul-item-{id}.

Groupable elements such as items in a list should be identified over group locators held in the class name attribute. While in most of the cases the container for a group such as a list parent can be identified by a unique identifier, all the children (list items) can be grouped together by the same class definition. Ideally this class definition also has an block indicator of its parent element.

_Example:_
```html
<ul id="my-list">
  <li id="my-list__item-{id of item 1}" class="my-list__item">Item 1</li>
  <li id="my-list__item-{id of item 2}" class="my-list__item">Item 2</li>
</ul>
```

If your locators would be in conflict with your style definitions - which should not be likely to be the case - a good choise to go with instead are data attributes. Data attributes can be defined free and are valid HTML. An example for a locator attribute name could be data-test-locator. The same rules as above apply here as well. Elements which are unique by nature shall have a unique identifier. Where it is possible to group elements contextually, use the same locator for them.
 
_Example:_
```html
<ul data-test-locator="my-list">
  <li data-test-locator="my-list__item">Item 1</li>
  <li data-test-locator="my-list__item">Item 2</li>
</ul>
```

You will find other opinions out there where the authors prefer the data attributes over id and class attributes. We are the opinion that if you have to write templates and tests in a devops manner, there is - in most cases - no need to introduce data attributes. In case you have to update values for example in an id field, it is also your responsibility to update the locators in the test codes. Adding additional data attributes is just like code duplication.

**Conclusion:**
* Use id attribute wherever possible
* Use class definitions for siblings in a group
* Use data attributes as a fallback (data-test-locator, data-test-id, data-test-group, ...)

## What about text as locators
It is highly not recommended to use text as locators in your test codes. A simple wording update of a text in the UI will result in a broken locator in your test. Another reason not to use text locators is if you deal with multi-language interfaces. If you want to test your application in all languages, that would lead to having to write tests for each language in order to tackle the text challenge. The recommendation is to wrap text in a parent element with a proper identifier such as a unique id and check the presence of the parent element.

_Example:_
```html
<span id="my-text">
  This is my fancy text which may change.
</span>
```

## How to build locators
Inspired by the [BEM methodology](http://getbem.com/) locators can be built with block scopes and appended element definitions.
A simple save button identifier could look like save--button. If the save button is inside the scope of a manage area it would look like manage-area__save--button. If the whole block would be scoped under a product it would result in product__manage-area__save--button.

The appended element type is technically not required to uniquely identify an element but makes your test codes easier to read. With the appendix it is easy to figure which type of element is to be dealt with.

**Conclusion:**
* separate blocks/scopes by __ (double underscore)
* append element type via -- (double dash)

