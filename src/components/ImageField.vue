<template>
    <el-image
        class="table-td-thumb"
        :src="getTemplate"
        :preview-src-list="[getTemplate]"
    ></el-image>
</template>

<script>
    export default {
        name: 'image-field',
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

<style scoped>
    .table-td-thumb {
        display: block;
        margin: auto;
        width: 40px;
        height: 40px;
    }
</style>