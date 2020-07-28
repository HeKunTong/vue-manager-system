<template>
    <el-dialog :title="title" :visible="visible" :width="width" @close="close">
        <el-form ref="form" :model.sync="form" label-width="70px">
            <template v-for="field in fields">
                <el-form-item v-if="!field.slot" :label="field.label">
                    <el-input v-if="!field.type || field.type === 'text'" v-model="form[field.key]"></el-input>
                    <el-input v-if="field.type === 'number'" type="number" v-model="form[field.key]"></el-input>
                    <el-input v-if="field.type === 'password'" type="password" v-model="form[field.key]"></el-input>
                    <el-select v-else-if="field.type === 'select'" v-model="form[field.key]" :placeholder="'请选择' + field.label">
                        <el-option v-for="(option, optionIndex) in field.options" :key="optionIndex" :label="option[field.optionText || 'text']" :value="option[field.optionKey || 'key']"></el-option>
                    </el-select>
                    <el-date-picker
                            v-else-if="field.type === 'date'"
                            :type="field.dateType"
                            :placeholder="'选择' + field.label"
                            v-model="form[field.key]"
                            :value-format="field.dateFormat || 'yyyy-MM-dd'"
                    ></el-date-picker>
                    <el-time-picker
                            v-else-if="field.type === 'time'"
                            :type="field.dateType"
                            :placeholder="'选择' + field.label"
                            v-model="form[field.key]"
                            :value-format="field.dateFormat || 'HH:mm:ss'"
                    ></el-time-picker>
                    <el-cascader v-else-if="field.type === 'cascader'" :options="field.options" v-model="form[field.key]" :show-all-levels="false"></el-cascader>
                    <el-switch
                            v-else-if="field.type === 'switch'"
                            v-model="form[field.key]"
                            :active-color="field.activeColor || '#13ce66'"
                            :inactive-color="field.inactiveColor || '#c0ccda'"
                            :active-text="field.activeText"
                            :inactive-text="field.inactiveText"
                            :active-value="field.activeValue || true"
                            :inactive-value="field.inactiveValue || false"
                    ></el-switch>
                    <el-checkbox-group v-else-if="field.type === 'checkbox'" v-model="form[field.key]">
                        <el-checkbox v-for="(option, optionIndex) in field.options" :key="optionIndex" :label="option[field.optionKey || 'key']">
                            {{ option[field.optionText || 'text'] }}
                        </el-checkbox>
                    </el-checkbox-group>
                    <el-radio-group v-else-if="field.type === 'radio'" v-model="form[field.key]">
                        <el-radio v-for="(option, optionIndex) in field.options" :key="optionIndex" :label="option[field.optionKey || 'key']">
                            {{ option[field.optionText || 'text'] }}
                        </el-radio>
                    </el-radio-group>
                    <el-input v-else-if="field.type === 'textarea'" type="textarea" :rows="field.row || 5" v-model="form[field.key]"></el-input>
                    <el-upload
                            v-else-if="field.type === 'upload'"
                            :action="field.action"
                            list-type="picture-card"
                            :multiple="false"
                            :on-preview="preview"
                            :on-remove="remove">
                        <i class="el-icon-plus"></i>
                    </el-upload>
                    <slot v-else :name="field.key"></slot>
                </el-form-item>
                <slot v-else :name="field.key"></slot>
            </template>
        </el-form>
        <el-dialog :visible.sync="dialogVisible">
            <img width="100%" :src="thumb" alt="">
        </el-dialog>
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
            fields: Array,
            visible: {
                type: [Boolean, String],
                default: false
            }
        },
        data() {
            return {
                dialogVisible: false,
                thumb: null,
            };
        },
        created() {
            for (let i in this.fields) {
                if (this.fields[i].type === 'checkbox' && !Array.isArray(this.form[this.fields[i].key])) {
                    this.form[this.fields[i].key] = [];
                }
            }
        },
        methods: {
            preview: function(file) {
                this.dialogVisible = true;
                this.thumb = file.url;
            },
            remove: function(file, fileList) {
                console.log(file, fileList);
            },
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