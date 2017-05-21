# Angular Phonecat Tutorial Focus on Karma Unit Tests and E2E Tests
* Unit tests - for testing values and JS code
    * Master component
        * Confirm component loads in HTTP data
        * Confirm default values are set - in selects, checkmarks
    * Detail components
        * Confirm loads in single HTTP data
    * Confirm custom filters returning correct bools or values
    * Confirm ustom services fetching from mock API in HTTP

* E2E tests - for testing templates, the DOM, and app interaction (components, controllers, services...)
    * Confirm URL redirects
    * Master component
        * Confirm filtering data from search boxes
        * Confirm ordering data by list orderers and sorters
        * Confirm clicking links return proper URLs
    * Detail components
        * Confirm proper views are routed to by checking model values
        * Confirm image paths correct in HTML src attributes
        * Confirm carousels in repeaters display correct image paths

## Karma Unit Tests - testing values and JS code
### Config File - karma.config.js
* disable strict mode
* use module.exports, pass in function with config
* use config.set with {}
* specify basePath
* specify bower dependencies, exclude bower and top-level specs
* set autoWatch to true for reloading
* include jasmine framework as framework dependency
* specify browsers
* add karma plugins - chrome-launcher, firefox-launcher, karma-jasmine

```
// karma.conf.js
//jshint strict: false // disables strict mode

module.exports = function(config) { // export module, function pass in config
  config.set({ // config.set pass in object

    basePath: './app', // specify basePath

    files: [ // specify bower dependencies, exclude bower specs, exclude top-level specs
      'bower_components/angular/angular.js',
      'bower_components/angular-mocks/angular-mocks.js',
      '**/*.module.js',
      '*!(.module|.spec).js',
      '!(bower_components)/**/*!(.module|.spec).js',
      '**/*.spec.js'
    ],

    autoWatch: true, // enable autoWatch for reloading

    frameworks: ['jasmine'], // include jasmine framework

    browsers: ['Chrome', 'Firefox'], // specify browsers

    plugins: [ // add karma plugins
      'karma-chrome-launcher',
      'karma-firefox-launcher',
      'karma-jasmine'
    ]

  });
};
```

### Karma Unit Tests - Simple Controller Unit Test
* test housed in describe block
    * arg1 = pass in name of controller
    * arg2 = function
* beforeEach to include parent module
* it block to describe function and have testing funciton
    * 'should' syntax to describe the test
    * inject $controller 
* mocked data - mock scope as empty object
* pass in mocked scope to $controller, reference controller name
* expect().toBe() pass in object to be an expected value

```
// app/phone-list/phone-list.component.spec.js
'use strict';
// describe block for unit test, pass in controller name and cb
describe('PhoneListController', function() {

  beforeEach(module('phonecatApp')); // beforeEach to include module

  // it block to describe test, inject in controller reference
  it('should create a `phones` model with 3 phones', inject(function($controller) {

    // mock out scope
    var scope = {};

    // grab controller and pass in mocked scope
    var ctrl = $controller('PhoneListController', {$scope: scope});

    // name expected( object ).toBe( value ) 
    expect(scope.phones.length).toBe(3);
  }));

});
```

### Karma Unit Tests - Simple Component Unit Test

* describe block, add name of component
* load module housing component before each unit test
* inner describe to test controller
* it should statement, inject $componentController
* pass in reference to controller into $componentController
* expect statement to be value

```
// app/phone-list/phone-list.component.spec.js
'use strict';

describe('phoneList', function() { // describe block, add name of component

  // load module housing component before each unit test
    // no need to pull in the parent appModule / phonecatApp module
  beforeEach(module('phonecatApp'));

  // inner describe to test the controller
  describe('PhoneListController', function() {

    // it should statement, inject $componentController
    it('should create a `phones` model with 3 phones', inject(function($componentController) {
      // pass in reference to controller into $componentController
      var ctrl = $componentController('phoneList');

      // expect statement to be value
      expect(ctrl.phones.length).toBe(3);
    }));
  });
});
```


## E2E Tests - testing templates, the DOM, and app interaction (components, services, controllers, ...)

### Structure
* e2e-tests
    * protractor.conf.js
    * README.md
    * scenerios.js

### Basic protractor.conf.js
* jshint strict to false
* add config to exports with {}
* allScriptsTimeout to 11s
* specify specs with wildcard .js
* specify browsers in capabilities obj
* specify baseUrl for local server
* specify jasmine framework
* specify jasmineNodeOpts defaultTimeoutInterval to 30000

```
// e2e-tests/protractor.conf.js

//jshint strict: false // jshint strict to false
exports.config = { // add config to exports with {}

  allScriptsTimeout: 11000, // allScriptsTimeout to 11s

  specs: [ // specify specs with wildcard .js
    '*.js'
  ],

  capabilities: { // specify browsers in capabilities obj
    'browserName': 'chrome'
  },

  baseUrl: 'http://localhost:8000/', // specify baseUrl for local server

  framework: 'jasmine', // specify jasmine framework

  jasmineNodeOpts: { // specify jasmineNodeOpts defaultTimeoutInterval to 30000
    defaultTimeoutInterval: 30000
  }

};

# Tests by Functional Design
## Input Filter Unit and E2E Tests

### Karma Unit Test with input filter
* describe block, add name of component
* load module housing component before each unit test
* inner describe to test controller
* it should statement, inject $componentController
* pass in reference to controller into $componentController
* expect statement to be value

```
// app/phone-list/phone-list.component.spec.js
'use strict';

describe('phoneList', function() { // describe block, add name of component

  // load module housing component before each unit test
    // no need to pull in the parent appModule / phonecatApp module
  beforeEach(module('phonecatApp'));

  // inner describe to test the controller
  describe('PhoneListController', function() {

    // it should statement, inject $componentController
    it('should create a `phones` model with 3 phones', inject(function($componentController) {
      // pass in reference to controller into $componentController
      var ctrl = $componentController('phoneList');

      // expect statement to be value
      expect(ctrl.phones.length).toBe(3);
    }));
  });
});
```

### E2E Test with input filter
# Basic scenerios.js
* describe block to nest tests
* inner describe block to test view
* beforeEach with browser.get to get index.html
* test filtering
    * specify behavior with it block
    * set up data with phonelist, element.all 
    * specity by.repeater('item in $ctrl.items') for ng-repeat
    * grab model by element(by.model('$ctr.query'))
    * use expect().tobe() for default values
    * mock input with query.sendKeys('search-term')
    * use expect().toBe() -- phoneList.count() function
    * clear query with query.clear(), repeat test

```
// e2e-tests/scenerios.js
'use strict';

// Angular E2E Testing Guide:
// https://docs.angularjs.org/guide/e2e-testing

describe('PhoneCat Application', function() {

  describe('phoneList', function() {

    beforeEach(function() {
      browser.get('index.html');
    });

    it('should filter the phone list as a user types into the search box', function() {
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));

      // Test 1. default value test
      expect(phoneList.count()).toBe(3);

      // Test 2. filter by 'nexus'
      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      // Test 3. Clear values, filter by 'motorola'
      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(2);
    });

  });

});
```

## Input Filter and Select Ordering Unit and E2E Tests

### Karma Unit test with input filter and ordering select box
* describe the component
* load the proper module in beforeEach
* internally test the controller
* make scoped var ctrl to be reset with beforeEach
* beforeEach inject the $componentController
    * reset the ctrl to be a new instance with $componentController
* test 1 use it should block, specify statement - 3 default, and pass in cb
    * expect list.length toBe 3
* test 2 use it should block, specify statement - default orderProp 'age'
    * expect ctrl.orderProp toBe 'age'

```
// app/phone-list/phone-list.component.spec.js
'use strict';

describe('phoneList', function() {

  // Load the module that contains the `phoneList` component before each test
  beforeEach(module('phoneList'));

  // Test the controller
  describe('PhoneListController', function() {
    var ctrl;

    beforeEach(inject(function($componentController) {
      ctrl = $componentController('phoneList');
    }));

    it('should create a `phones` model with 3 phones', function() {
      expect(ctrl.phones.length).toBe(3);
    });

    it('should set a default value for the `orderProp` model', function() {
      expect(ctrl.orderProp).toBe('age');
    });

  });
});

### E2E Unit test with input filter and ordering select box
* describe block for entire app
* describe block for phoneList component
* beforeEach to browser.get index.html
* 2 it blocks
    // These tests seen pointless since it is confirming AngularJS functionality, and take more time to write than just toggle in the browsers
    * test for filtering phone list when types into searchbox
        * setup repeater with element.all(by.repeater('item in items'))
        * setup query with element(by.model('$ctrl.query'))
        * expect default value toBe something
        * query.sendKeys('searchterm') to mock searchterm input
        * expect toBe test
        * query.clear(), repeat

    * test for controlling phone order via dropdown menu
        * grab elements with elemnt(by.model('$ctrl.<model-name>'))
            * grab query with element(by.model('$ctrl.query'))
            * grab orderer with element(by.model('$ctrl.orderProp'))
        * CSS to manipulate - orderSelect.element(by.css('option[value="name"]')) 
            * orderSelect.element(by.css('option[value="name"]'));
        * get all elements with element.all(by.repeater('item in $ctrl.items')).column('item.name')
            * element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));
        
        * internal function to getNames, returns mapped column, which returns element's getText()
        ```
        function getNames() {
            return phoneNameColumn.map(function(elem) {
            return elem.getText();
            });
        }
        ```
        *   narrow the dataset to make the assertions shorter
        ```
        queryField.sendKeys('tablet');
        ```
        * expect statement, toEqual pass in array of expected values
        ``` 
        expect(getNames()).toEqual([
            'Motorola XOOM\u2122 with Wi-Fi',
            'MOTOROLA XOOM\u2122'
        ]);
        ```
        * simulate click -- ???
        ```
        nameOption.click();
        ```
        * expect statment after click
        ```
        expect(getNames()).toEqual([
            'MOTOROLA XOOM\u2122',
            'Motorola XOOM\u2122 with Wi-Fi'
        ]);
        ```

```
// e2e-tests/scenerios.js
'use strict';

describe('PhoneCat Application', function() {

  describe('phoneList', function() {

    beforeEach(function() {
      browser.get('index.html');
    });

    it('should filter the phone list as a user types into the search box', function() {
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));

      expect(phoneList.count()).toBe(3);

      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(2);
    });

    it('should be possible to control phone order via the drop-down menu', function() {
      var queryField = element(by.model('$ctrl.query'));
      var orderSelect = element(by.model('$ctrl.orderProp'));
      var nameOption = orderSelect.element(by.css('option[value="name"]')); // grab name from select, prepare to click below
      var phoneNameColumn = element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));

      function getNames() {
        return phoneNameColumn.map(function(elem) {
          return elem.getText();
        });
      }

      queryField.sendKeys('tablet');   // Let's narrow the dataset to make the assertions shorter

      expect(getNames()).toEqual([
        'Motorola XOOM\u2122 with Wi-Fi',
        'MOTOROLA XOOM\u2122'
      ]);

      nameOption.click();

      expect(getNames()).toEqual([
        'MOTOROLA XOOM\u2122',
        'Motorola XOOM\u2122 with Wi-Fi'
      ]);
    });
  });
});
```


## Simple HTTP Backend Unit Test
### Karma Unit Test Backend
* use $httpBackend for testing
* start with describe block with component name
* beforeEach, arg of module name
* inner describe to test controller
* prep 2 vars - $httpBackend and ctrl
* beforeEach to inject $componentController and _$httpBackend_
    * the _ on ends are ignored, but allow for reassignment to our $httpBackend variable
    ```
        ...
        beforeEach(inject(function($componentController, _$httpBackend_) {
            $httpBackend = _$httpBackend_;
        ...
    ```
    * have $httpBackend to expectGET('<our json file>')'
        * .respond for mock .then, pass in json structure [{},{}] mock data
        ```
        $httpBackend.expectGET('phones/phones.json')
                    .respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
        ```
    * set ctrl to $componentController('phoneList');
* first test - it should block to test default no values, then populate
    * use expect().toBeUndefined();
    ```
    expect(ctrl.phones).toBeUndefined();
    ```
    * mock a backend call with $httpBackend.flush()
    ```
    $httpBackend.flush();
    ```
    * Expect model list toEqual values from the .respond() mock call
    ```
    expect(ctrl.phones).toEqual([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
    ```
* second test - it should block to test default orderProp model value
    *** Basic way to test default values of model
    ```
    expect(ctrl.modelname).toBe('defaultvalue');
    ```

```
// app/phone-list/phone-list.component.spec.js
'use strict';

describe('phoneList', function() {

  // Load the module that contains the `phoneList` component before each test
  beforeEach(module('phoneList'));

  // Test the controller
  describe('PhoneListController', function() {
    var $httpBackend, ctrl;

    // The injector ignores leading and trailing underscores here (i.e. _$httpBackend_).
    // This allows us to inject a service and assign it to a variable with the same name
    // as the service while avoiding a name conflict.
    beforeEach(inject(function($componentController, _$httpBackend_) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/phones.json')
                  .respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);

      ctrl = $componentController('phoneList');
    }));

    it('should create a `phones` property with 2 phones fetched with `$http`', function() {
      expect(ctrl.phones).toBeUndefined();

      $httpBackend.flush();
      expect(ctrl.phones).toEqual([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
    });

    it('should set a default value for the `orderProp` property', function() {
      expect(ctrl.orderProp).toBe('age');
    });
  });
});
```

### E2E Test Backend
* Since this is a mock call from the backend, the Unit Test primarily handles this
* the only thing to change for an E2E http test is the total number of results from a call
```
expect(phoneList.count()).toBe(20);
```
*** If you don't know an exact number for a returned JSON object, you can use
```
expect(phoneList.count()).toBeGreaterThan(0);
expect(phoneList.count()).toBeLessThan(200);
```

```
// e2e-tests/scenerios.js
'use strict';

// Angular E2E Testing Guide:
// https://docs.angularjs.org/guide/e2e-testing

describe('PhoneCat Application', function() {

  describe('phoneList', function() {

    beforeEach(function() {
      browser.get('index.html');
    });

    it('should filter the phone list as a user types into the search box', function() {
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));

      expect(phoneList.count()).toBe(20);

      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(8);
    });

    it('should be possible to control phone order via the drop-down menu', function() {
      var queryField = element(by.model('$ctrl.query'));
      var orderSelect = element(by.model('$ctrl.orderProp'));
      var nameOption = orderSelect.element(by.css('option[value="name"]'));
      var phoneNameColumn = element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));

      function getNames() {
        return phoneNameColumn.map(function(elem) {
          return elem.getText();
        });
      }

      queryField.sendKeys('tablet');   // Let's narrow the dataset to make the assertions shorter

      expect(getNames()).toEqual([
        'Motorola XOOM\u2122 with Wi-Fi',
        'MOTOROLA XOOM\u2122'
      ]);

      nameOption.click();

      expect(getNames()).toEqual([
        'MOTOROLA XOOM\u2122',
        'Motorola XOOM\u2122 with Wi-Fi'
      ]);
    });
  });
});
```

## Testing Link Paths 
### Karma Unit Test - no need since this requires testing clicks and URL Paths
### E2E Test - Click URL Paths
* Other tests are the same as above
* it should block
    * first filter down with query, element by model and grab the model variable
    * query.sendKeys to whittle down the list
    * grab the anchor tags !!! test where there will only be 1 results
        * element.all by.css selector, first() click()
        ```
        element.all(by.css('.phones li a')).first().click();
        ```
    * expect statement, browser.getLocationAbsUrl() .tobe('proper/path')
    ```
    expect(browser.getLocationAbsUrl()).toBe('/phones/nexus-s');
    ```
```
    it('should render phone specific links', function() {
      var query = element(by.model('$ctrl.query'));
      query.sendKeys('nexus');

      element.all(by.css('.phones li a')).first().click();
      expect(browser.getLocationAbsUrl()).toBe('/phones/nexus-s');
    });
```

```
// e2e-tests/scenerios.js
'use strict';

// Angular E2E Testing Guide:
// https://docs.angularjs.org/guide/e2e-testing

describe('PhoneCat Application', function() {

  describe('phoneList', function() {

    beforeEach(function() {
      browser.get('index.html');
    });

    it('should filter the phone list as a user types into the search box', function() {
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));

      expect(phoneList.count()).toBe(20);

      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(8);
    });

    it('should be possible to control phone order via the drop-down menu', function() {
      var queryField = element(by.model('$ctrl.query'));
      var orderSelect = element(by.model('$ctrl.orderProp'));
      var nameOption = orderSelect.element(by.css('option[value="name"]'));
      var phoneNameColumn = element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));

      function getNames() {
        return phoneNameColumn.map(function(elem) {
          return elem.getText();
        });
      }

      queryField.sendKeys('tablet');   // Let's narrow the dataset to make the assertions shorter

      expect(getNames()).toEqual([
        'Motorola XOOM\u2122 with Wi-Fi',
        'MOTOROLA XOOM\u2122'
      ]);

      nameOption.click();

      expect(getNames()).toEqual([
        'MOTOROLA XOOM\u2122',
        'Motorola XOOM\u2122 with Wi-Fi'
      ]);
    });

    it('should render phone specific links', function() {
      var query = element(by.model('$ctrl.query'));
      query.sendKeys('nexus');

      element.all(by.css('.phones li a')).first().click();
      expect(browser.getLocationAbsUrl()).toBe('/phones/nexus-s');
    });

  });
});
```


## Testing Routes
### Unit Testing Routes
* all that needs to be done here is add angular-route.js to the files array
```
// karma.conf.js
//jshint strict: false
module.exports = function(config) {
  config.set({

    basePath: './app',

    files: [
      'bower_components/angular/angular.js',
      'bower_components/angular-route/angular-route.js', // add here
      'bower_components/angular-mocks/angular-mocks.js',
      '**/*.module.js',
      '*!(.module|.spec).js',
      '!(bower_components)/**/*!(.module|.spec).js',
      '**/*.spec.js'
    ],

    autoWatch: true,

    frameworks: ['jasmine'],

    browsers: ['Chrome', 'Firefox'],

    plugins: [
      'karma-chrome-launcher',
      'karma-firefox-launcher',
      'karma-jasmine'
    ]

  });
};
```

### E2E Testing Routes
* Top-level redirect *** outside inner describe ***
* it block
* browser.get index.html
* expect browser.getLocationAbsUrl() toBe main route /phones
```
  it('should redirect `index.html` to `index.html#!/phones', function() {
    browser.get('index.html');
    expect(browser.getLocationAbsUrl()).toBe('/phones');
  });
```

* Add new describe block to test phone detail route
* beforeEach.get to get a test route
* it block
    * expect element by.binding $ctrl.phoneID .getText toBe value

```
  describe('View: Phone detail', function() {

    beforeEach(function() {
      browser.get('index.html#!/phones/nexus-s');
    });

    it('should display placeholder page with `phoneId`', function() {
      expect(element(by.binding('$ctrl.phoneId')).getText()).toBe('nexus-s');
    });

  });
```

```
// e2e-tests/scenerios.js
'use strict';

// Angular E2E Testing Guide:
// https://docs.angularjs.org/guide/e2e-testing

describe('PhoneCat Application', function() {
  // main route test
  it('should redirect `index.html` to `index.html#!/phones', function() {
    browser.get('index.html');
    expect(browser.getLocationAbsUrl()).toBe('/phones');
  });

  describe('View: Phone list', function() {

    beforeEach(function() {
      browser.get('index.html#!/phones');
    });

    it('should filter the phone list as a user types into the search box', function() {
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));

      expect(phoneList.count()).toBe(20);

      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(8);
    });

    it('should be possible to control phone order via the drop-down menu', function() {
      var queryField = element(by.model('$ctrl.query'));
      var orderSelect = element(by.model('$ctrl.orderProp'));
      var nameOption = orderSelect.element(by.css('option[value="name"]'));
      var phoneNameColumn = element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));

      function getNames() {
        return phoneNameColumn.map(function(elem) {
          return elem.getText();
        });
      }

      queryField.sendKeys('tablet');   // Let's narrow the dataset to make the assertions shorter

      expect(getNames()).toEqual([
        'Motorola XOOM\u2122 with Wi-Fi',
        'MOTOROLA XOOM\u2122'
      ]);

      nameOption.click();

      expect(getNames()).toEqual([
        'MOTOROLA XOOM\u2122',
        'Motorola XOOM\u2122 with Wi-Fi'
      ]);
    });

    it('should render phone specific links', function() {
      var query = element(by.model('$ctrl.query'));
      query.sendKeys('nexus');

      element.all(by.css('.phones li a')).first().click();
      expect(browser.getLocationAbsUrl()).toBe('/phones/nexus-s');
    });

  });

  // Route test
  describe('View: Phone detail', function() {

    beforeEach(function() {
      browser.get('index.html#!/phones/nexus-s');
    });

    it('should display placeholder page with `phoneId`', function() {
      expect(element(by.binding('$ctrl.phoneId')).getText()).toBe('nexus-s');
    });
  });
});
```

## Testing Child Components and Routes
### Karma Unit Tests - Testing Child Components and Routes
* open with describe block, name of module/component
* beforeEach include submodule (using modular architecture)
* describe the Controller
* declare the $httpBackend and ctrl vars

* beforeEach to inject the $component, _$httpBackend_, and $routeParams
    * set $httpBackend to _$httpBackend_
    * $httpBackend expectGet fake route, respond with fake object
    * set $routeParams.phoneId to same fake name
    * set ctrl to $componentController, pass in name of component (since is unnamed controller in cmp)
```
    beforeEach(inject(function($componentController, _$httpBackend_, $routeParams) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/xyz.json').respond({name: 'phone xyz'});

      $routeParams.phoneId = 'xyz';

      ctrl = $componentController('phoneDetail');
    }));
```
* it statement
    * expect ctrl.phone toBeUndefined()
    * do mock $httpBackend.flush() call
    * expect ctrl.phone toEqual mock object
```
    it('should fetch the phone details', function() {
      expect(ctrl.phone).toBeUndefined();

      $httpBackend.flush();
      expect(ctrl.phone).toEqual({name: 'phone xyz'});
    });
```

```
// app/phone-detail/phone-detail.component.spec.js
'use strict';

describe('phoneDetail', function() {

  // Load the module that contains the `phoneDetail` component before each test
  beforeEach(module('phoneDetail'));

  // Test the controller
  describe('PhoneDetailController', function() {
    var $httpBackend, ctrl;

    beforeEach(inject(function($componentController, _$httpBackend_, $routeParams) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/xyz.json').respond({name: 'phone xyz'});

      $routeParams.phoneId = 'xyz';

      ctrl = $componentController('phoneDetail');
    }));

    it('should fetch the phone details', function() {
      expect(ctrl.phone).toBeUndefined();

      $httpBackend.flush();
      expect(ctrl.phone).toEqual({name: 'phone xyz'});
    });
  });
});
```


### E2E Tests - Testing Child Components and Routes
* Test to navigate to route, confirm title on page is correct
* describe block for new detail view
    * beforeEach to set up browser.get proper url 'index.html#!/phones/nexus-s'
    * it block
        * expect element by.binding $ctrl.phone.name getText() to be Nexus S

```
  // e2e-tests/scenerios.js
  ...
  describe('View: Phone detail', function() {

    beforeEach(function() {
      browser.get('index.html#!/phones/nexus-s');
    });

    it('should display the `nexus-s` page', function() {
      expect(element(by.binding('$ctrl.phone.name')).getText()).toBe('Nexus S');
    });

  });
  ...
```

## Testing Custom Filters
* This test is for an app-wide filter that toggles a checkmark
```
'use strict';

angular.
  module('core').
  filter('checkmark', function() {
    return function(input) {
      return input ? '\u2713' : '\u2718';
    };
  });
```

### Karma Unit Tests - Testing Custom Filters
* describe block to set it up
* beforeEach to include module
* it block, describe functionality
    * all in an inject statement, inject the checkmarkFilter
        * expect checkmarkFilter(true) toBe proper value
        * expect checkmarkFilter(false) toBe proper value

```
// checkmark/checkmark.filter.spec.js
'use strict';

describe('checkmark', function() {

  beforeEach(module('core'));

  it('should convert boolean values to unicode checkmark or cross',
    inject(function(checkmarkFilter) {
      expect(checkmarkFilter(true)).toBe('\u2713');
      expect(checkmarkFilter(false)).toBe('\u2718');
    })
  );

});

```

### E2E Tests - Testing Custom Filters - no need since this is not determined by browser interaction


## Testing Event Handlers
### Karma Unit Tests - refactor phone-detail url
* in describe block, mock out a phone detail response
```
    var xyzPhoneData = {
      name: 'phone xyz',
      images: ['image/url1.png', 'image/url2.png']
    };
```
* in beforeEach block, have $httpBackend expectGet from json respond with mock object
```
    ...
    $httpBackend.expectGET('phones/xyz.json').respond(xyzPhoneData);
    ...
```
* in fetch phone details test, expect ctrl.phone toEqual the mock object
```
    ...
    expect(ctrl.phone).toEqual(xyzPhoneData);
    ...
```

```
// app/phone-detail/phone-detail.component.spec.js
'use strict';

describe('phoneDetail', function() {

  // Load the module that contains the `phoneDetail` component before each test
  beforeEach(module('phoneDetail'));

  // Test the controller
  describe('PhoneDetailController', function() {
    var $httpBackend, ctrl;
    var xyzPhoneData = {
      name: 'phone xyz',
      images: ['image/url1.png', 'image/url2.png']
    };

    beforeEach(inject(function($componentController, _$httpBackend_, $routeParams) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/xyz.json').respond(xyzPhoneData);

      $routeParams.phoneId = 'xyz';

      ctrl = $componentController('phoneDetail');
    }));

    it('should fetch the phone details', function() {
      expect(ctrl.phone).toBeUndefined();

      $httpBackend.flush();
      expect(ctrl.phone).toEqual(xyzPhoneData);
    });
  });
});
```

### E2E Tests - Testing Event Handlers
* Test 1 - display the first phone image as the main phone image
    * grab the mainImage with element(by.css('selector'))
    * expect the getAttribute('src').toMatch(escaped regex path)
     *** only escape slashes, not dots
```
    ...
    it('should display the first phone image as the main phone image', function() {
      var mainImage = element(by.css('img.phone'));

      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });
    ...

```
* Test 2 - display the first phone image as the main phone image
    * grab the mainImage with element(by.css('selector'))
    * grab thumnails with element.all(by.css('selecor'))
    * get the index and mock click that image
    * expect the getAttribute src of the mainImage toMatch 'img/phones/nexus-s.{index}.jpg'
```
    ...
    it('should display the first phone image as the main phone image', function() {
      var mainImage = element(by.css('img.phone'));
      var thumbnails = element.all(by.css('.phone-thumbs img'));

      thumbnails.get(2).click();
      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.2.jpg/);

      thumbnails.get(0).click();
      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });
    ...
```

```
/// e2e-tests/scenerios.js
'use strict';

// Angular E2E Testing Guide:
// https://docs.angularjs.org/guide/e2e-testing

describe('PhoneCat Application', function() {

  it('should redirect `index.html` to `index.html#!/phones', function() {
    browser.get('index.html');
    expect(browser.getLocationAbsUrl()).toBe('/phones');
  });

  describe('View: Phone list', function() {

    beforeEach(function() {
      browser.get('index.html#!/phones');
    });

    it('should filter the phone list as a user types into the search box', function() {
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));

      expect(phoneList.count()).toBe(20);

      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(8);
    });

    it('should be possible to control phone order via the drop-down menu', function() {
      var queryField = element(by.model('$ctrl.query'));
      var orderSelect = element(by.model('$ctrl.orderProp'));
      var nameOption = orderSelect.element(by.css('option[value="name"]'));
      var phoneNameColumn = element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));

      function getNames() {
        return phoneNameColumn.map(function(elem) {
          return elem.getText();
        });
      }

      queryField.sendKeys('tablet');   // Let's narrow the dataset to make the assertions shorter

      expect(getNames()).toEqual([
        'Motorola XOOM\u2122 with Wi-Fi',
        'MOTOROLA XOOM\u2122'
      ]);

      nameOption.click();

      expect(getNames()).toEqual([
        'MOTOROLA XOOM\u2122',
        'Motorola XOOM\u2122 with Wi-Fi'
      ]);
    });

    it('should render phone specific links', function() {
      var query = element(by.model('$ctrl.query'));
      query.sendKeys('nexus');

      element.all(by.css('.phones li a')).first().click();
      expect(browser.getLocationAbsUrl()).toBe('/phones/nexus-s');
    });

  });

  describe('View: Phone detail', function() {

    beforeEach(function() {
      browser.get('index.html#!/phones/nexus-s');
    });

    it('should display the `nexus-s` page', function() {
      expect(element(by.binding('$ctrl.phone.name')).getText()).toBe('Nexus S');
    });

    it('should display the first phone image as the main phone image', function() {
      var mainImage = element(by.css('img.phone'));

      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });

    it('should swap the main image when clicking on a thumbnail image', function() {
      var mainImage = element(by.css('img.phone'));
      var thumbnails = element.all(by.css('.phone-thumbs img'));

      thumbnails.get(2).click();
      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.2.jpg/);

      thumbnails.get(0).click();
      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });
  });
});
```

## Testing ngResource and Custom Services
### Karma Unit Testing ngResource and Custom Services
* Update karma.conf.js to include new angular-resource 
```
// karma.conf.js
...
files: [
  'bower_components/angular/angular.js',
  'bower_components/angular-resource/angular-resource.js',
  ...
],
...
```

* Test to verify new service issues HTTP requests and returns future objects
* describe Phone service block
    * Setup variables $httpBackend, Phone, mockdata phonesData
        ```
        var $httpBackend;
        var Phone;
        var phonesData = [
            {name: 'Phone X'},
            {name: 'Phone Y'},
            {name: 'Phone Z'}
        ];
        ```
    * Add a custom equality tester before each test with jasmine.addCustomEqualityTester(angular.equals);
        * angular.equals as equals testing standard, which ignores res object methods $- prefixed props
        ```
        beforeEach(function() {
            jasmine.addCustomEqualityTester(angular.equals);
        });
        ```
    * Load the module that contains the `Phone` service before each test
        ```
        beforeEach(module('core.phone'));
        ```
    * Instantiate the service and "train" `$httpBackend` before each test
        ```
        beforeEach(inject(function(_$httpBackend_, _Phone_) {
            $httpBackend = _$httpBackend_;
            $httpBackend.expectGET('phones/phones.json').respond(phonesData);

            Phone = _Phone_;
        }));
        ```
    * Verify that there are no outstanding expectations or requests after each test
        ```
        afterEach(function () {
            $httpBackend.verifyNoOutstandingExpectation();
            $httpBackend.verifyNoOutstandingRequest();
        });
        ```
    * it block
        * set up phones to Phone.query()
        * ??? why expect phones toEqual [] ??? already did query ???
        * mock call to $httpBackend.flush()
        * now expech phones toEqual phonesData from above

        ```
        it('should fetch the phones data from `/phones/phones.json`', function() {
            var phones = Phone.query();

            expect(phones).toEqual([]);

            $httpBackend.flush();
            expect(phones).toEqual(phonesData);
        });

        ```

```
// app/core/phone/phone.service.spec.js
'use strict';

describe('Phone', function() { // describe Phone service block
  // Setup variables $httpBackend, Phone, mockdata phonesData
  var $httpBackend;
  var Phone;
  var phonesData = [
    {name: 'Phone X'},
    {name: 'Phone Y'},
    {name: 'Phone Z'}
  ];

  // Add a custom equality tester before each test ???
  beforeEach(function() {
      // angular.equals as equals testing standard, which ignores res object methods $- prefixed props
    jasmine.addCustomEqualityTester(angular.equals);
  });

  // Load the module that contains the `Phone` service before each test
  beforeEach(module('core.phone'));

  // Instantiate the service and "train" `$httpBackend` before each test
  beforeEach(inject(function(_$httpBackend_, _Phone_) {
    $httpBackend = _$httpBackend_;
    $httpBackend.expectGET('phones/phones.json').respond(phonesData);
    // ??? why this order - why is Phone here / last ???
    Phone = _Phone_;
  }));

  // Verify that there are no outstanding expectations or requests after each test
  afterEach(function () {
    $httpBackend.verifyNoOutstandingExpectation();
    $httpBackend.verifyNoOutstandingRequest();
  });

  it('should fetch the phones data from `/phones/phones.json`', function() {
    var phones = Phone.query();

    expect(phones).toEqual([]);

    $httpBackend.flush();
    expect(phones).toEqual(phonesData);
  });
});
```

### E2E Testing ngResource and Custom Services - none since no user interactions
