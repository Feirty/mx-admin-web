<template>
    <div class="table">
        <div class="container" v-loading="loading">
            <div class="handle-box">
                <el-form :inline="true" >
                    <el-form-item label="菜单名称">
                        <el-input placeholder=""></el-input>
                    </el-form-item>
                    <el-form-item label="菜单权限码">
                        <el-input placeholder=""></el-input>
                    </el-form-item>
                    <el-form-item label="菜单层级">
                        <el-select v-model="select_word" placeholder="请选择">
                        <el-option label="一级" value="1"></el-option>
                        <el-option label="二级" value="2"></el-option>
                        <el-option label="三级" value="3"></el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="search">查询</el-button>
                        <el-button  @click="search" label-position="right" style="float:right "> 重置</el-button>
                    </el-form-item>
                    <el-form-item class="toRight">
                        <el-button type="primary" @click="search">新建</el-button>
                    </el-form-item>
                </el-form>
            </div>
            <el-table :data="tableData" border class="table" ref="multipleTable" @selection-change="handleSelectionChange">
                <el-table-column type="selection" width="50" align="center"></el-table-column>
                <el-table-column prop="name" label="名称" align="center" >
                </el-table-column>
                <el-table-column prop="menuIcon" label="图标" align="center" >
                </el-table-column>
                <el-table-column prop="menuUrl" label="路由" align="center" >
                </el-table-column>
                <el-table-column prop="menuCode" label="权限码" show-overflow-tooltip align="center" >
                </el-table-column>
                <el-table-column prop="sort" label="排序" align="center" width="50">
                </el-table-column>
                <el-table-column label="操作"  align="center" width="120">
                    <template slot-scope="scope">
                        <el-button type="text" icon="el-icon-edit" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                        <el-button type="text" icon="el-icon-delete" class="red" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination background @size-change="handleSizeChange" @current-change="handleCurrentChange" border :current-page.sync="pageNo" :page-sizes="[10, 20, 50]" :page-size="pageSize" layout="sizes, prev, pager, next" :total="entityCount">
                </el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="50px">
                <el-form-item label="日期">
                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date" value-format="yyyy-MM-dd" style="width: 100%;"></el-date-picker>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="地址">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>

            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 删除提示框 -->
        <el-dialog title="提示" :visible.sync="delVisible" width="300px" center>
            <div class="del-dialog-cnt">删除不可恢复，是否确定删除？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="delVisible = false">取 消</el-button>
                <el-button type="primary" @click="deleteRow">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: 'basetable',
        data() {
            return {
                url: './vuetable.json',
                tableData: [],
                entityCount: 1,
                pageNo: 1,
                pageSize: 10,
                loading:false,
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                del_list: [],
                is_search: false,
                editVisible: false,
                delVisible: false,
                form: {
                    name: '',
                    date: '',
                    address: ''
                },
                idx: -1
            }
        },
        created() {
            this.getData();
        },
        computed: {
            data() {
               
            }
        },
        methods: {
            handleCurrentChange(val) {
                this.pageNo = val;
                this.getData();
            },
            getData() {
                this.loading=true;
                this.$api.menu.list(
                    {
                        pageNo: this.pageNo,
                        pageSize: this.pageSize
                    }
                ).then(res=> {
                    this.tableData = res.data.data.entities;
                    this.entityCount = res.data.data.entityCount;
                    this.loading=false;
                })
            },
            search() {
                this.is_search = true;
            },
            formatter(row, column) {
                return row.address;
            },
            filterTag(value, row) {
                return row.tag === value;
            },
            handleEdit(index, row) {
                this.idx = index;
                const item = this.tableData[index];
                this.form = {
                    name: item.name,
                    date: item.date,
                    address: item.address
                }
                this.editVisible = true;
            },
            handleDelete(index, row) {
                this.idx = index;
                this.delVisible = true;
            },
            delAll() {
                const length = this.multipleSelection.length;
                let str = '';
                this.del_list = this.del_list.concat(this.multipleSelection);
                for (let i = 0; i < length; i++) {
                    str += this.multipleSelection[i].name + ' ';
                }
                this.$message.error('删除了' + str);
                this.multipleSelection = [];
            },
            handleSizeChange: function (pageSize) {
                if (!this.msgListLoading) {
                    this.pageNo = 1
                    this.pageSize = pageSize
                    this.getData();
                }
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
            },
            // 保存编辑
            saveEdit() {
                this.$set(this.tableData, this.idx, this.form);
                this.editVisible = false;
                this.$message.success(`修改第 ${this.idx+1} 行成功`);
            },
            // 确定删除
            deleteRow(){
                this.tableData.splice(this.idx, 1);
                this.$message.success('删除成功');
                this.delVisible = false;
            }
        }
    }

</script>

<style scoped>
    .handle-box {
        margin-bottom: 0px;
    }
    .toRight {
        float:right;
        margin-right:0px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }
    .del-dialog-cnt{
        font-size: 16px;
        text-align: center
    }
    .table{
        width: 100%;
        font-size: 14px;
    }
    .red{
        color: #ff0000;
    }
    .mr10{
        margin-right: 10px;
    }
</style>
