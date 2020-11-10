<!--  -->
<template>
<div class=''>

    <el-dialog title="学习计划" :visible.sync="dialogVisible" width="50%">
        <el-table :data="dataList" border>
            <el-table-column align="center" prop="plantime" label="时间规划"></el-table-column>
            <el-table-column align="center" prop="planname" label="姓名"></el-table-column>
            <el-table-column align="center" prop="page" label="年龄"></el-table-column>
            <el-table-column align="center" prop="kemu" label="科目"></el-table-column>
            <el-table-column align="center" prop="target" label="目标"></el-table-column>
        </el-table>
        <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
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
            dialogVisible: false,
            dataList: []
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        //获取学员得学习计划
        getStudentplan(id) {
            this.dialogVisible = true
            this.$http({
                url: this.$http.adornUrl('/tigger/stayplan/list'),
                method: 'get',
                params: this.$http.adornParams({
                    uid: id
                })
            }).then(({
                data
            }) => {
                console.log(data)
                this.dataList = data.page
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
    activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>
