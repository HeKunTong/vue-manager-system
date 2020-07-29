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
                <el-button
                        type="primary"
                        icon="el-icon-lx-down"
                        :loading="loading"
                        @click="handleDownload"
                >导出</el-button>
            </div>
            <xdd-table
                    ref="xddTable"
                    :data="tableData"
                    :columns="columns"
                    :current="query.pageIndex"
                    :size="query.pageSize"
                    :total="pageTotal"
                    @change="handleSelectionChange"
            />
        </div>

        <xdd-form
                :title="type === 1 ? '添加' : '编辑'"
                :form="form"
                :visible="visible"
                :fields="fields"
                @save="save"
                @close="close"
        >
            <el-form-item slot="address" label="地址">
                <el-input v-model="form.address"></el-input>
            </el-form-item>
        </xdd-form>
    </div>
</template>

<script>

    import XddTable from '@/components/Table';
    import XddForm from '@/components/Form';
    import { fetchData } from '@/api';
    import * as excel from '@/utils/exportToExcel';
    import forEach from 'lodash/forEach';

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
                loading: false,
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
                fields: [{
                    key: 'name',
                    type: 'text',
                    label: '用户名'
                }, {
                    key: 'address',
                    slot: true
                }, {
                    key: 'phone',
                    type: 'number',
                    label: '手机号码'
                }, {
                    key: 'password',
                    type: 'password',
                    label: '密码'
                }, {
                    key: 'status',
                    type: 'select',
                    label: '状态',
                    optionKey: 'key',
                    optionText: 'text',
                    options: [{
                        key: 0,
                        text: '全部'
                    }, {
                        key: 1,
                        text: '正常'
                    }, {
                        key: 2,
                        text: '禁用'
                    }]
                }, {
                    key: 'date',
                    type: 'date',
                    dateType: 'date',
                    dateFormat: 'yyyy-MM-dd',
                    label: '日期',
                }, {
                    key: 'time',
                    type: 'time',
                    dateType: 'date',
                    dateFormat: 'HH:mm:ss',
                    label: '时间',
                }, {
                    key: 'city',
                    type: 'cascader',
                    label: '城市',
                    options: [{
                        value: 'guangdong',
                        label: '广东省',
                        children: [{
                            value: 'guangzhou',
                            label: '广州市',
                            children: [{
                                value: 'tianhe',
                                label: '天河区'
                            }, {
                                value: 'haizhu',
                                label: '海珠区'
                            }]
                        }, {
                            value: 'dongguan',
                            label: '东莞市',
                            children: [{
                                value: 'changan',
                                label: '长安镇'
                            }, {
                                value: 'humen',
                                label: '虎门镇'
                            }]
                        }]
                    }, {
                        value: 'hunan',
                        label: '湖南省',
                        children: [{
                            value: 'changsha',
                            label: '长沙市',
                            children: [{
                                value: 'yuelu',
                                label: '岳麓区'
                            }]
                        }]
                    }]
                }, {
                    key: 'switch',
                    type: 'switch',
                    label: '开关',
                }, {
                    key: 'fruit',
                    type: 'checkbox',
                    label: '水果',
                    optionKey: 'key',
                    optionText: 'text',
                    options: [{
                        key: 0,
                        text: '香蕉'
                    }, {
                        key: 1,
                        text: '苹果'
                    }, {
                        key: 2,
                        text: '葡萄'
                    }]
                }, {
                    key: 'education',
                    type: 'radio',
                    label: '学历',
                    options: [{
                        key: 0,
                        text: '初中'
                    }, {
                        key: 1,
                        text: '高中'
                    }, {
                        key: 2,
                        text: '本科'
                    }, {
                        key: 3,
                        text: '专科'
                    }]
                }, {
                    key: 'content',
                    type: 'textarea',
                    label: '内容',
                }, {
                    key: 'thumb',
                    type: 'upload',
                    label: '缩略图',
                    action: 'https://jsonplaceholder.typicode.com/posts/',
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
                this.form = {
                    fruit: []
                };
                this.type = 1;
                this.visible = true;
            },
            // 编辑操作
            handleEdit: function (row, index) {
                row.fruit = Array.isArray(row.fruit) ? row.fruit : [];
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
            },
            handleDownload: function() {
                if (this.multipleSelection.length !== 0) {
                    this.loading = true
                    const tHeader = ['Id', '用户名', '账户余额', '地址', '状态', '注册时间']
                    const filterVal = ['id', 'name', 'money', 'address', 'state', 'date']
                    const list = this.multipleSelection;
                    let data = [];
                    forEach (list, function(item, index) {
                        data[index] = [];
                        for (let i in filterVal) {
                            let key = filterVal[i];
                            let str = item[key];
                            if (key === 'money') {
                                str = '￥' + str;
                            }
                            data[index].push(str);
                        }
                    });
                    excel.export_json_to_excel({
                        header: tHeader,
                        data,
                        filename: ''
                    });
                    this.$refs.xddTable.clearSelection();
                    this.loading = false;
                } else {
                    this.$message({
                        message: 'Please select at least one item',
                        type: 'warning'
                    })
                }
            },
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
