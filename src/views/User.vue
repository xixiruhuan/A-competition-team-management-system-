<template>
    <div>
        <div style="margin: 10px 0">

            <el-input style="width: 100px;margin-left: 5px" placeholder="请输入年级" v-model="grade">
            </el-input>

            <el-input style="width: 200px;margin-left: 5px" placeholder="请输入学号" suffix-icon="el-icon-search" v-model="username">
            </el-input>

            <el-button style="margin-left: 5px" type="pimary"@click="load">搜索</el-button>
        </div>
        <div style="margin: 10px 0">
            <el-button type="primary"@click="handleAdd">新增<i class="el-icon-circle-plus-outline"></i></el-button>
            <el-upload action="https://jsonplaceholder.typicode.com/posts/" :show-file-list="false" accept="xlsx" style="display: inline-block">
            <el-button type="primary"style="margin-left: 10px">导入<i class="el-icon-download"></i></el-button>
            </el-upload>
            <el-button type="primary" @click="exp" style="margin-left: 10px">导出<i class="el-icon-upload2"></i></el-button>
        </div>
        <el-table :data="tableData" border stripe>
            <el-table-column prop="id" label="ID" width="40"></el-table-column>
            <el-table-column prop="username" label="用户名" width="140"></el-table-column>
            <el-table-column prop="password" label="密码" width="140"></el-table-column>
            <el-table-column prop="grade" label="年级" width="60"></el-table-column>
            <el-table-column prop="major" label="专业" width="140"></el-table-column>
            <el-table-column prop="name" label="姓名" width="140"></el-table-column>
            <el-table-column prop="phone" label="电话 " width="140"></el-table-column>
            <el-table-column prop="qq" label="QQ号码" width="140"></el-table-column>
            <el-table-column prop="email" label="邮箱" width="140"></el-table-column>

            <el-table-column prop="do" label="操作">
                <template slot-scope="scope" >
                    <el-button type="success" @click="handleUpdate(scope.row)">编辑</el-button>
                    <el-popconfirm
                            style="margin-left:8px "
                            confirm-button-text='好的'
                            cancel-button-text='不用了'
                            icon="el-icon-info"
                            icon-color="red"
                            title="这是一段内容确定删除吗？"
                            @confirm="del(scope.row.id)"
                    >
                        <el-button type="danger"slot="reference">删除</el-button>

                    </el-popconfirm>
                </template>
            </el-table-column>
        </el-table>
        <div style="padding: 10px 0">
            <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="pageNum"
                    :page-sizes="[5, 10, 15, 20]"
                    :page-size="pageSize"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="total">
            </el-pagination>

        </div>

        <el-dialog title="用户信息" :visible.sync="dialogFormVisible" width="30%" >
            <el-form label-width="80px" size="small">
                <el-form-item label="用户名">
                    <el-input style="width: 205px" v-model="form.username" autocomplete="off" type="number"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input style="width: 205px" v-model="form.password" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="年级">
                    <el-input style="width: 205px" v-model="form.grade" autocomplete="off" type="number"></el-input>
                </el-form-item>
                <el-form-item label="专业">
                    <el-input style="width: 205px" v-model="form.major" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input style="width: 205px" v-model="form.name" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="电话">
                    <el-input style="width: 205px" v-model="form.phone" autocomplete="off" type="number"></el-input>
                </el-form-item>
                <el-form-item label="QQ号码">
                    <el-input style="width: 205px" v-model="form.qq" autocomplete="off"  type="number"></el-input>
                </el-form-item>
                <el-form-item label="邮箱">
                    <el-input style="width: 205px" v-model="form.email" autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="save">保存</el-button>
            </div>
        </el-dialog>



    </div>
</template>

<script>
    export default {
        name: "User",
        data(){
            return{
                tableData:[],
                dialogFormVisible:false,
                form:{},
                total:0,
                pageNum:1,
                pageSize:5,
                username:"",
                grade:"",
                chart: null, // echarts图表实例
            }
        },

        created() {
            //请求分页数据
            this.load();


        },
        methods:{

            load(){
                this.request.get("http://localhost:9090/user/page",{
                    params:{
                        pageNum: this.pageNum,
                        pageSize:this.pageSize,
                        username:this.username,
                        grade:this.grade
                    }
                }).then(res=>{
                    console.log(res)
                    this.tableData = res.data
                    this.total=res.total

                })

            },
            handleAdd(){
                this.dialogFormVisible=true
                this.form={}
            },
            handleUpdate(row){
                this.dialogFormVisible=true
                this.form=row
            },
            save(){
                this.request.post("http://localhost:9090/user",this.form).then(res => {
                    if (res) {
                        this.$message.success("保存成功")
                        this.dialogFormVisible = false
                        this.load()
                    } else {
                        this.$message.error("保存失败")
                    }
                })
            },
            del(id){
                this.request.delete("http://localhost:9090/user/"+id).then(res => {
                    if (res) {
                        this.$message.success("删除成功")
                        this.dialogFormVisible = false
                        this.load()
                    } else {
                        this.$message.error("删除失败")
                    }
                })
            },

            handleSizeChange(pageSize){
                this.pageSize=pageSize
                this.load();
            },
            handleCurrentChange(pageNum){
                this.pageNum=pageNum
                this.load();
            },
            exp(){
                window.open("http://localhost:9090/user/export")
            },


        }
    }
</script>

<style scoped>

</style>