[![NPM version](https://img.shields.io/npm/v/angular-container.svg)](https://www.npmjs.org/package/angular-container)
![](https://img.shields.io/npm/dm/angular-container.svg)
![](https://img.shields.io/github/last-commit/opteo/angular-container.svg)
# Angular Container

Here at Opteo, we recently undertook the process of updating our large Angular.js based front-end application (composed of many controllers, services and directives) to Vue, and 
as migrating our entire application was expected to take several weeks (which later became months), we built the <b>angular-container</b> Vue component as a simple way to use our existing Angular pages within our new Vue based app.

### Features
- Use existing Angular based controllers/services/directives within `.vue` components
- Allows for an easier migration process to Vue when working with a large existing Angular codebase
- Can be lazily loaded for only pages which require Angular

### Notes:
By default, non curly braces are used for rendering angular-container `$scope` variables, and instead square brackets are used (this can be overwritten). 
This is due to Vue also using curly braces for data binding, which would cause mounting issues and conflicts.

### Installation
An installation of Angular.js is required for this component to work correctly. This can be done via yarn or npm:
```bash
yarn add angular angular-container
```

### Example
```vue
<template>
  <AngularContainer ref="ng" name="user" ctrl="userCtrl">
    <div ng-for="item in items">
      <span ng-click="addItem()">[[ item ]]</span>
      
      <!-- Existing custom Angular directives can be used -->
      <blue>This users the "blue" directive</blue>
      
      <!-- Vue variables can be used within the Angular instance -->
      <span>{{ hello }}</span>
    </div>
  </AngularContainer>
</template>
<script>
import AngularContainer from "angular-container"

export default {
  name: "User",
  data() {
    return {
      hello: "world",
    }
  },
  mounted() {
    const angularContainer = this.$refs.ng
    angularContainer.init() // Instantiate the Angular.js instance after Vue has mounted
    const angularInstance = angularContainer.getInstance()
    
    /* Use controllers, services and directives that you need */
    angularInstance.service("userService", ["$injector", ($injector)  => {
      return {
        getSomeData() {
          return fetch("https://example.com/api/")
        }
      }
    }])
    
    // Controller    
    angularInstance.controller("userCtrl", ["$scope", "userService", ($scope, userService) => {
      $scope.items = [1, 2, 3, 4, 5]
      $scope.addItem = () => $scope.items.push(Math.random())
      const data = await userService.getSomeData()
    }])
    
    // Directive
    angularInstance.directive("blue", () => {
      return {
        restrict: "E",
        transclude: true,
        template: `<span style="color:blue;" ng-transclude></span>`
      }
    })
  },
  components: {
    AngularContainer,
  }
}
</script>
```
