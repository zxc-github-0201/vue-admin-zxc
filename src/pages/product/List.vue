<template>
    <div>
        <el-button type="success" @click="toAddHandler">添加</el-button>
        <el-button type="danger">批量删除</el-button>

        <!-- 表格 -->
        <el-table :data="products">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="categoryId" label="所属产品"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格 -->
         <!-- 模态框--> 
        <el-dialog
            title="添加产品信息"
            :visible.sync="visible"
            width="60%">
            <!-- {{form}} -->
            <el-form :model="form" label-width="80px">
                <el-form-item label="名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input v-model="form.price"></el-input>
                </el-form-item>
                <el-form-item label="所属栏目">
                    <el-select v-model="form.categoryId" placeholder="请选择">
                        <el-option v-for="item in options"
                            :key="item.value" :label="item.name"
                            :value="item.parentId">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="介绍">
                    <el-input v-model="form.description"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
            <el-button size="small" @click="closeModalHandler">取 消</el-button>
            <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
             </span>
        </el-dialog>
        <!-- /模态框--> 
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        loadData(){
            let url = "http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
            //将查询结果设置到product中,this指向外部函数的this
            this.products = response.data;
        })
        },
        submitHandler(){
            //this.form 对象  ---字符串--->后台{type:'customer,age:12}
            //json字符串 '{"type":"customer","age":12}'
            //request.post(url,this.form)
            //查询字符串 type=customer&age=12
            //通过request与后台进行交互，并且要携带参数
            let url = "http://localhost:6677/product/saveOrUpdate";
            // request.post(url,this.form);
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //模态框关闭
                this.closeModalHandler();
                //刷新
                this.loadData;
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toDeleteHandler(){
            // 确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
             confirmButtonText: '确定',
            cancelButtonText: '取消',
             type: 'warning'
            }).then(() => {
             this.$message({
            type: 'success',
            message: '删除成功!'
             });
            })
        },
        toUpdateHandler(){
            this.visible = true;
        },
        closeModalHandler(){
            this.visible = false;
        },
        toAddHandler(){
            this.visible = true;
        }
    },
    data(){
        return{
            visible:false,
            products:[],
            form:{
                
            }
        }
    },
    created(){
            //在页面加载出来时加载数据
            this.loadData();
        },
}
</script>
<style scoped>

</style>