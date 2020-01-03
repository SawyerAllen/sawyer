<template>
    <div>
        <!-- 按钮开始 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- 按钮结束 -->
        <!-- 表格开始 -->
        <el-table :data="employees">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column width="150" prop="telephone" label="手机号"></el-table-column>
            <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
            <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot"> 
                    <a href="" @click.prevent = "toDeleteHandler(slot.row.realname)">删除</a>
                    <a href="" @click.prevent = "toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- 表格结束 -->
        <!-- 分页开始 -->
        <el-pagination
            layout="prev, pager, next" :total="50">
        </el-pagination>
        <!-- /分页开始 -->
        <!-- 模态框开始 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            测试：{{form}}
            <el-form :model="form" label-width="80">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"/>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"/>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.realname"/>
                </el-form-item>
                <el-form-item label="性别">
                    <el-radio-group v-model="form.gender">
                        <el-radio label="男">男</el-radio>
                        <el-radio label="女">女</el-radio>
                    </el-radio-group>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"/>
                </el-form-item>
                <el-form-item label="身份证号">
                    <el-input v-model="form.idCard"/>
                </el-form-item>
                <el-form-item label="银行卡号">
                    <el-input v-model="form.bankCard"/>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <!-- @click === onClick -->
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 模块框结束 -->
    </div>
</template>

<script>
import request from '@/utils/request' //资源库
import querystring from 'querystring' //系统库
export default {
    
    //用于存放网页中需要调用的方法
    methods:{
        submitHandler(){
            let url = "http://localhost:6677/waiter/saveOrUpdate";
            //前端向后台发送请求,完成数据的保存工作
            request({
                url,
                method:"post",
                headers:{
                    //告诉给后台我的请求体重放的是查询字符串
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求中的数据，将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModalHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        //重载员工数据
        loadData(){
            //this->vue实例，通过vue实例访问该实例中的数据,methods中的实例方法以及data中的返回值
            let url = "http://localhost:6677/waiter/findAll"
            //get方式不需要考虑参数格式，get传递的一般为jason格式
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.employees = response.data;
            })
        },
        toDeleteHandler(name){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除“' + name +"”的信息成功！"
          });
        })

        },
        toUpdateHandler(row){
            this.title = "修改员工信息"
            this.visible = true;
        },
        closeModalHandler(){
            this.visible = false;
    },
        toAddHandler(){
            this.title = "录入员工信息"
            this.visible = true;
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return {
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        //页面加载出来的时候加载数据
        this.loadData();
    },
    
}

    
</script>

<style scoped>

</style>