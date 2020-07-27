<template>
    <el-tag :type="getStatus">{{getTemplate}}</el-tag>
</template>

<script>
    export default {
        name: 'tag-field',
        props: {
            name: String,
            row: Object,
            status: Function,
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
            getStatus: function() {
                if (this.status) {
                    let value = this.getTemplate;
                    return this.status(value);
                } else {
                    return null;
                }
            }
        }
    };
</script>

<style scoped>

</style>