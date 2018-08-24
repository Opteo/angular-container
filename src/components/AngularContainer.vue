<template>
    <div :ng-controller="ctrl"
        :id="name"
        :ref="`ng__${name}`">
        <slot>Angular app instance must be used or removed.</slot>
    </div>
</template>
<script>
import angular from 'angular'

export default {
    name: 'AngularContainer',
    props: {
        name: {
            type: String,
            required: true,
        },
        ctrl: {
            type: String,
            required: true,
            validator(value) {
                return value.substr(value.length - 4) === 'Ctrl'
            },
        },
    },
    data() {
        return {
            app: null,
        }
    },
    methods: {
        init(curly = false) {
            const appContainer = this.$refs[`ng__${this.name}`]
            let settings = []

            if (!curly) {
                settings = [
                    '$interpolateProvider',
                    $interpolateProvider => {
                        $interpolateProvider.startSymbol('[[')
                        $interpolateProvider.endSymbol(']]')
                    },
                ]
            }

            this.app = angular.module(this.name, [], settings)

            angular.element(() => {
                angular.bootstrap(appContainer, [this.name], {
                    strictDi: true,
                })
            })
        },
        forceUpdate() {
            this.$forceUpdate()
        },
        getInstance() {
            return this.app
        },
    },
    destroyed() {
        if (!this.app) {
            this.app.run($rootScope => $rootScope.$destroy())
        }
    },
}
</script>
