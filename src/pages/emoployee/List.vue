<template>
    <div>
        <!-- 按钮 -->
        <div>
       <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
       <el-button type="danger" size="small">批量删除</el-button>
    </div>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="employees">
        <el-table-column label="编号" prop="id" fixed="left"></el-table-column>
        <el-table-column label="姓名" prop="realname" fixed="left"></el-table-column>
        <el-table-column label="用户名" prop="username" ></el-table-column>
        <el-table-column label="性别" prop="gender"></el-table-column>
        <el-table-column label="手机号" prop="telephone" width="120"></el-table-column>
        <el-table-column label="身份证号" prop="idCard" width="200"></el-table-column>
        <el-table-column label="银行卡号" prop="bankCard" width="200"></el-table-column>
        <el-table-column label="操作" fixed="right">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>  
        </el-table-column>
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50">
    </el-pagination>
        <!-- /分页 -->
        <!-- 模态框 -->
         <el-dialog 
         :title="title" 
         :visible.sync="visible" 
         width="60%">
         {{form}}
    <el-form v-model="form" label-width="80px">
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
            <el-radio :label ="男" AutoSize="true">男</el-radio>
            <el-radio :label ="女" AutoSize="true">女</el-radio>
           </el-radio-group>
        </el-form-item>
        <el-form-item label="手机号" >
            <el-input v-model="form.telephone"/>
        </el-form-item>
        <el-form-item label="身份证号" >
            <el-input v-model="form.idCard"/>
        </el-form-item>
        <el-form-item label="银行卡号">
            <el-input  v-model="form.bankCard"/>
        </el-form-item>
    </el-form>
     <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
        <!-- /模态框 -->
    </div>
</template>

<script>
import request from '@/utils/request'  //自定义库
import querystring from 'querystring' //系统库
export default {
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        //再页面加载出来的的时候加载数据
        this.loadData();
    },
    methods:{
        submitHandler(){
            let url = "http://localhost:6677/waiter/saveOrUpdate"
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method : "post",
                //告诉后台我的请求体中放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据，将this.form转换为查询字符串发送给后台的
                data:querystring.stringify(this.form)
            }).then((Response)=>{
                 // 模态框关闭
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
            //this->vue实例，通过vue实例访问该实例中的数据
            //this.title  /  this.toAddHandler
            let url = "http://localhost:6677/waiter/findAll"
            request.get(url).then((Response)=>{
                this.employees = Response.data;
            })
        },
        toDeleteHandler(id){
            //确认？
            this.$confirm('此操作将永久删除该成员, 是否继续？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url="http://localhost:6677/employee/deleteById?id="+id;
            request.get(url).then((response)=>{
                this.loadData();
                this.$message({
            type: 'success',
            message: '成功删除'+id+'号顾客'
          });
            })
          
        })
        },
        toUpdateHandler(row){
            this.form = row;
            this.title = "修改员工信息";
            this.visible=true;
        },
        toAddHandler(row){
            this.title = "添加员工信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        }

    }

}
</script>

<style scoped>

</style>