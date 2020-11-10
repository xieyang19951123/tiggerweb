<!--  -->
<template>
<div class=''>
    <el-dialog width="70%" title="家长信息" :visible.sync="disaVisible">
        <el-table :data="datalist" border="">
            <el-table-column label="与孩子得关系" prop="childs" align="center"></el-table-column>
            <el-table-column label="姓名" prop="pname" align="center"></el-table-column>
            <el-table-column label="年龄" prop="page" align="center"></el-table-column>
            <el-table-column label="工作地址" prop="addresswork" align="center"></el-table-column>
            <el-table-column label="电话" prop="piphone" align="center"></el-table-column>
            <el-table-column label="兴趣" prop="intersing" align="center"></el-table-column>
            <el-table-column label="兼职或创业意向" prop="partjob" align="center"></el-table-column>
        </el-table>
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
            disaVisible: false,
            datalist: []
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        getdatalist(id) {
            this.disaVisible = true
            this.$http({
                url: this.$http.adornUrl('/tigger/parent/list'),
                method: 'get',
                params: this.$http.adornParams({
                    uid: id
                })
            }).then(({
                data
            }) => {
                console.log(data)
                if (data.code == 0) {
                    this.datalist = data.page
                }
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
