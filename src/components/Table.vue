<template>
    <div>
        <el-table
                :data="data"
                border
                class="table"
                ref="multipleTable"
                header-cell-class-name="table-header"
                @selection-change="change"
        >
            <el-table-column v-if="selectable" type="selection" width="55" align="center"></el-table-column>
            <el-table-column
                    v-for="(column, index) in columns"
                    :key="index"
                    :prop="column.key"
                    :label="column.title"
                    :width="column.width"
                    :align="column.align"
            >
                <template slot-scope="scope">
                    <text-field v-if="!column.type || column.type === 'text'" :name="column.key" :func="column.template" :row="scope.row"/>
                    <image-field v-else-if="column.type === 'image'" :name="column.key" :func="column.template" :row="scope.row"/>
                    <tag-field v-else-if="column.type === 'tag'" :name="column.key" :status="column.status" :func="column.template" :row="scope.row"/>
                    <action-field v-else-if="column.type === 'action'" :name="column.key" :index="scope.$index" :buttons="column.buttons" :func="column.template" :row="scope.row"/>
                </template>
            </el-table-column>
        </el-table>
        <div v-if="needPage" class="pagination">
            <el-pagination
                    background
                    layout="total, prev, pager, next"
                    :current-page="current"
                    :page-size="size"
                    :total="total"
                    @current-change="changePage"
            ></el-pagination>
        </div>
    </div>
</template>

<script>
    import TextField from '@/components/TextField';
    import ImageField from '@/components/ImageField';
    import TagField from '@/components/TagField';
    import ActionField from '@/components/ActionField';

    export default {
        name: 'xdd-table',
        components: {
            TextField,
            ImageField,
            TagField,
            ActionField,
        },
        props: {
            data: {
                type: Array,
                default: []
            },
            selectable: {
                type: [Boolean, String],
                default: true
            },
            columns: {
                type: Array,
                default: []
            },
            needPage: {
                type: [Boolean, String],
                default: true
            },
            current: [String, Number],
            size: [String, Number],
            total: [String, Number],
        },
        methods: {
            change: function (selection) {
                this.$emit('change', selection);
            },
            changePage: function(current) {
                this.$emit('changePage', current);
            }
        }
    };
</script>

<style scoped>
    .table {
        width: 100%;
        font-size: 14px;
    }
</style>