<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 用户管理
                </el-breadcrumb-item>
                <el-breadcrumb-item>用户列表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button
                        type="primary"
                        icon="el-icon-delete"
                        class="handle-del mr10"
                        @click="delAllSelection"
                >批量删除</el-button>
                <el-select v-model="query.address" placeholder="地址" class="handle-select mr10">
                    <el-option key="1" label="广东省" value="广东省"></el-option>
                    <el-option key="2" label="湖南省" value="湖南省"></el-option>
                </el-select>
                <el-input v-model="query.name" placeholder="用户名" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="el-icon-search" @click="handleSearch">搜索</el-button>
                <el-button type="primary" icon="el-icon-lx-add" @click="handleAdd">添加</el-button>
            </div>
            <xdd-table
                :data="tableData"
                :columns="columns"
                :current="query.pageIndex"
                :size="query.pageSize"
                :total="pageTotal"
            />
        </div>

        <xdd-form :title="type === 1 ? '添加' : '编辑'" :form.sync="form" :visible="visible" @save="save" @close="close"/>
    </div>
</template>

<script>

    import XddTable from '@/components/Table';
    import XddForm from '@/components/Form';
    import { fetchData } from '@/api';

    export default {
        components: {
            XddTable,
            XddForm,
        },
        data() {
            return {
                query: {
                    address: '',
                    name: '',
                    pageIndex: 1,
                    pageSize: 10
                },
                tableData: [],
                columns: [{
                    key: 'id',
                    title: 'ID',
                    width: 55,
                    align: 'center',
                }, {
                    key: 'name',
                    title: '用户名',
                }, {
                    key: 'money',
                    title: '账户余额',
                    template: function(data) {
                        return '￥' + data.money;
                    }
                }, {
                    key: 'thumb',
                    title: '头像(查看大图)',
                    type: 'image',
                    align: 'center'
                }, {
                    key: 'address',
                    title: '地址',
                }, {
                    key: 'state',
                    title: '状态',
                    type: 'tag',
                    align: 'center',
                    status: function(value) {
                        return value === '成功' ? 'success' : (value === '失败' ? 'danger' : '');
                    }
                }, {
                    key: 'date',
                    title: '注册时间',
                }, {
                    key: 'action',
                    title: '操作',
                    width: 180,
                    type: 'action',
                    align: 'center',
                    buttons: [{
                        title: '编辑',
                        icon: 'el-icon-edit',
                        onAction: this.handleEdit
                    }, {
                        title: '删除',
                        icon: 'el-icon-delete',
                        type: 'danger',
                        onAction: this.handleDelete
                    }]
                }],
                multipleSelection: [],
                delList: [],
                visible: false,
                pageTotal: 0,
                form: {},
                idx: -1,
                id: -1,
                type: null
            };
        },
        created() {
            this.getData();
        },
        methods: {
            // 获取 easy-mock 的模拟数据
            getData: function () {
                fetchData(this.query).then(res => {
                    this.tableData = res.list;
                    this.pageTotal = res.pageTotal || 50;
                });
            },
            // 触发搜索按钮
            handleSearch: function () {
                this.$set(this.query, 'pageIndex', 1);
                this.getData();
            },
            // 删除操作
            handleDelete: function (row, index) {
                // 二次确认删除
                this.$confirm('确定要删除吗？', '提示', {
                    type: 'warning'
                })
                    .then(() => {
                        this.$message.success('删除成功');
                        this.tableData.splice(index, 1);
                    })
                    .catch(() => {});
            },
            // 多选操作
            handleSelectionChange: function (val) {
                this.multipleSelection = val;
            },
            delAllSelection: function () {
                const length = this.multipleSelection.length;
                let str = '';
                this.delList = this.delList.concat(this.multipleSelection);
                for (let i = 0; i < length; i++) {
                    str += this.multipleSelection[i].name + ' ';
                }
                this.$message.error(`删除了${str}`);
                this.multipleSelection = [];
            },
            // 编辑操作
            handleAdd: function () {
                this.form = {};
                this.type = 1;
                this.visible = true;
            },
            // 编辑操作
            handleEdit: function (row, index) {
                this.idx = index;
                this.form = row;
                this.type = 2;
                this.visible = true;
            },
            // 保存编辑
            save: function () {
                console.log('form--------', this.form);
                this.visible = false;
                if (this.type === 1) {
                    this.$message.success(`数据添加成功`);
                } else {
                    this.$message.success(`修改第 ${this.idx + 1} 行成功`);
                    this.$set(this.tableData, this.idx, this.form);
                }
            },
            close: function() {
                this.visible = false;
            },
            // 分页导航
            handlePageChange: function (val) {
                this.$set(this.query, 'pageIndex', val);
                this.getData();
            }
        }
    };
</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }

    .mr10 {
        margin-right: 10px;
    }

</style>
