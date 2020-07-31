<template>
    <a class="xdd-link" :target="target" :href="getHref">{{ getTemplate }}</a>
</template>

<script>
    export default {
        name: 'link-field',
        props: {
            name: String,
            row: Object,
            target: {
                type: String,
                default: '_self'
            },
            href: Function,
            func: Function,
        },
        computed: {
            getTemplate: function() {
                let data = this.row;
                let value = data[this.name];
                if (this.func) {
                    return this.func(data);
                } else {
                    return value;
                }
            },
            getHref: function() {
                let data = this.row;
                let value = data[this.name];
                if (this.href) {
                    return this.href(data);
                } else {
                    return value;
                }
            }
        }
    };
</script>

<style scoped>
    .xdd-link {
        color: #409EFF;
    }
</style>