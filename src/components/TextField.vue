<template>
    <span>{{ getTemplate }}</span>
</template>

<script>
    export default {
        name: 'text-field',
        props: {
            name: String,
            row: Object,
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
            }
        }
    };
</script>