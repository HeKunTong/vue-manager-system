<template>
    <el-dialog :title="title" :visible="visible" :width="width" @close="close">
        <el-form ref="form" :model="form" label-width="70px">
            <el-form-item label="用户名">
                <el-input v-model="form.name"></el-input>
            </el-form-item>
            <el-form-item label="地址">
                <el-input v-model="form.address"></el-input>
            </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
            <el-button @click="close">取 消</el-button>
            <el-button type="primary" @click="save">确 定</el-button>
        </span>
    </el-dialog>
</template>

<script>
    export default {
        name: 'xdd-form',
        props: {
            title: String,
            width: {
                type: String,
                default: '40%',
            },
            form: Object,
            visible: {
                type: [Boolean, String],
                default: false
            }
        },
        methods: {
            save: function() {
                this.$emit('save');
            },
            close: function() {
                this.$emit('close');
            }
        }
    };
</script>

<style scoped>

</style>