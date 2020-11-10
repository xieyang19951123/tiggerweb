<!--  -->
<template>
<div class=''>
    <el-table :data="list" style="width: 100%" border>
        <el-table-column label="序号" type="index" align="center"></el-table-column>
        <el-table-column label="故障" align="center" prop="ruleName"></el-table-column>
        <el-table-column label="推荐人获得的积分" align="center" prop="parentPoint"></el-table-column>
        <el-table-column label="获得的积分" align="center" prop="point"></el-table-column>
        <el-table-column label="操作">
            <template slot-scope="scope">
                <el-button type="success" @click="dialogFormVisible=true,form = scope.row">修改 </el-button>
            </template>
        </el-table-column>
    </el-table>

    <el-dialog title="收货地址" :visible.sync="dialogFormVisible">
        <el-form :model="form">
            <el-form-item label="规则名">
                <el-input v-model="form.ruleName"></el-input>
            </el-form-item>
            <el-form-item label="推荐人获得的积分">
                <el-input-number v-model="form.parentPoint" :precision="2"></el-input-number>
            </el-form-item>
            <el-form-item label="获得的积分">
                <el-input-number v-model="form.point" :precision="2"></el-input-number>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="edit">确 定</el-button>
        </div>
    </el-dialog>
</div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';

export default {
    //import引入的组件需要注入到对象中才能使用
    components: {},
    data() {
        //这里存放数据
        return {
            list: [],
            dialogFormVisible: false,
            form: {}
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        //获取所有的数据
        getAllRule() {
            this.$http({
                url: this.$http.adornUrl("/tigger/rlue/list"),
                method: "get"
            }).then(({
                data
            }) => {
                //console.log(data)
                this.list = data.page
            })
        },
        edit() {
            //  console.log(this.form)
            this.$http({
                url: this.$http.adornUrl("/tigger/rlue/edit"),
                method: 'post',
                data: this.$http.adornData({
                    "id": this.form.id,
                    "parentPoint": this.form.parentPoint,
                    "point": this.form.point,
                    "ruleName": this.form.ruleName,
                    "showStatus": this.form.showStatus
                })
            }).then(({
                data
            }) => {
                this.dialogFormVisible = false
                this.getAllRule()

            })
        }

    },
    //生命周期 - 创建完成（可以访问当前this实例）
    created() {

    },
    //生命周期 - 挂载完成（可以访问DOM元素）
    mounted() {

    },
    beforeCreate() {}, //生命周期 - 创建之前
    beforeMount() {}, //生命周期 - 挂载之前
    beforeUpdate() {}, //生命周期 - 更新之前
    updated() {}, //生命周期 - 更新之后
    beforeDestroy() {}, //生命周期 - 销毁之前
    destroyed() {}, //生命周期 - 销毁完成
    activated() {
        this.getAllRule()
    }, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>
