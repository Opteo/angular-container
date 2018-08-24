<template>
    <div id="app">
        This is the Vue container
        <AngularContainer ref="ng"
            name="app"
            ctrl="appCtrl">
            This is inside the Angular instance container
            <div ng-repeat="item in items">
                <span ng-click="addItem()">[[ item ]]</span>
            </div>
            Vue binded data inside the Angular instance --> {{ hello }}
        </AngularContainer>
    </div>
</template>
<script>
import AngularContainer from 'angular-container'

export default {
    name: 'App',
    data() {
        return {
            hello: 'world',
        }
    },
    mounted() {
        const angularContainer = this.$refs.ng
        angularContainer.init()
        const angularInstance = angularContainer.getInstance()

        angularInstance.controller('appCtrl', [
            '$scope',
            $scope => {
                $scope.items = [1, 2, 3, 4, 5]
                $scope.addItem = () => {
                    $scope.items.push(Math.random())
                }
            },
        ])
    },
    components: {
        AngularContainer,
    },
}
</script>
<style>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
}
#nav {
    padding: 30px;
}

#nav a {
    font-weight: bold;
    color: #2c3e50;
}

#nav a.router-link-exact-active {
    color: #42b983;
}
</style>
