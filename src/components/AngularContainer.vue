<template>
    <div :ng-controller="ctrl"
        :id="name"
        :ref="`ng__${name}`"
        v-show="status">
        <slot>Angular app instance must be used or removed.</slot>
    </div>
</template>
<script>
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
        status: {
            type: Boolean,
            required: false,
        },
        curly: {
            type: Boolean,
            required: false,
            default: false,
        },
    },
    data() {
        return {
            app: null,
        }
    },
    methods: {
        init() {
            const appContainer = this.$refs[`ng__${this.name}`]
            let settings = []

            if (!this.curly) {
                settings = [
                    '$interpolateProvider',
                    $interpolateProvider => {
                        $interpolateProvider.startSymbol('[[')
                        $interpolateProvider.endSymbol(']]')
                    },
                ]
            }

            this.app = angularLib.module(this.name, [], settings)

            angular.element(() => {
                angular.bootstrap(appContainer, [this.name], {
                    strictDi: true,
                })
            })
        },
        forceUpdate() {
            this.$forceUpdate()
        },
    },
    destroyed() {
        if (!this.app) {
            this.app.run($rootScope => $rootScope.$destroy())
        }
    },
}
</script>
