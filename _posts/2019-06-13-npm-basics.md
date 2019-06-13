# what's npm
*npm*, short for Node Package Manager, is two things.
1. online repository
2. a command-line utility for interacting with said repository that aids in package installation, version management, and dependency management

# install package
npm install

# package.json
it's mainly used for managing dependencies.

# Creating a package.json file
npm init

# sample package.json
```javascript
{
  "name": "lrageyqaexo.angular",
  "version": "0.0.0",
  "private": true,
  "dependencies": {
    "rxjs": "6.5.2",
    "tslib": "1.9.3",
    "core-js": "2.6.9",
    "zone.js": "0.9.1",
    "jasmine-core": "2.99.1",
    "@angular/core": "8.0.0",
    "@angular/forms": "8.0.0",
    "@angular/common": "8.0.0",
    "@angular/router": "8.0.0",
    "jasmine-marbles": "0.5.0",
    "@angular/compiler": "8.0.0",
    "web-animations-js": "2.3.1",
    "@angular/animations": "8.0.0",
    "@angular/platform-browser": "8.0.0",
    "angular-in-memory-web-api": "0.8.0",
    "@angular/platform-browser-dynamic": "8.0.0"
  },
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "~0.10.0",
    "@angular/cli": "~7.0.2",
    "@angular/compiler-cli": "~7.0.0",
    "@angular/language-service": "~7.0.0",
    "@types/node": "~8.9.4",
    "@types/jasmine": "~2.8.8",
    "@types/jasminewd2": "~2.0.3",
    "codelyzer": "~4.5.0",
    "jasmine-core": "~2.99.1",
    "jasmine-spec-reporter": "~4.2.1",
    "karma": "~3.0.0",
    "karma-chrome-launcher": "~2.2.0",
    "karma-coverage-istanbul-reporter": "~2.0.1",
    "karma-jasmine": "~1.1.2",
    "karma-jasmine-html-reporter": "^0.2.2",
    "protractor": "~5.4.0",
    "ts-node": "~7.0.0",
    "tslint": "~5.11.0",
    "typescript": "~3.1.1"
  }
}
```