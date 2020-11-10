<!--  -->
<template>
<div class=''>
    <el-dialog title="学生档案" :visible.sync="dialogVisible" width="80%">
        <el-dialog width="50%" title="校外培训" :visible.sync="innerVisible" append-to-body>
            <el-table :data="innerdatalist" border="">
                <el-table-column label="科目名" prop="cname" align="center"></el-table-column>
                <el-table-column label="上课时间" prop="classTime" align="center"></el-table-column>
                <el-table-column label="教学方式" prop="teaching" align="center"></el-table-column>
                <el-table-column label="教学制度" prop="tsystem" align="center"></el-table-column>
                <el-table-column label="机构名" prop="oname" align="center"></el-table-column>
                <el-table-column label="教师名" prop="teacher" align="center"></el-table-column>
                <el-table-column label="开始时间" prop="startTime" align="center"></el-table-column>
                <el-table-column label="结束时间" prop="endTime" align="center"></el-table-column>
            </el-table>
        </el-dialog>
        <el-table :data="dataList" border>
            <el-table-column label="中文名" prop="zhName" align="center"></el-table-column>
            <el-table-column label="英文名" prop="enName" align="center"></el-table-column>
            <el-table-column label="出生年月日" prop="birth" align="center"></el-table-column>
            <el-table-column label="兴趣" prop="interest" align="center"></el-table-column>
            <el-table-column label="特长" prop="spec" align="center"></el-table-column>
            <el-table-column label="梦想" prop="dream" align="center"></el-table-column>
            <el-table-column label="电话" prop="iphone" align="center"></el-table-column>
            <el-table-column label="介绍人" prop="sponsor" align="center"></el-table-column>
            <el-table-column label="学校" prop="school" align="center"></el-table-column>
            <el-table-column label="健康" prop="heathl" align="center"></el-table-column>
            <el-table-column label="沟通方式" prop="kinkUp" align="center"></el-table-column>
            <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button type="text" @click="outschoole(scope.row.id)">校外培训</el-button>
                </template>
            </el-table-column>
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
            innerVisible: false,
            dataList: [],
            innerdatalist: []
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        getlist(id) {
            this.dialogVisible = true
            this.$http({
                url: this.$http.adornUrl('/tigger/child/getlist'),
                method: 'get',
                params: this.$http.adornParams({
                    uid: id
                })
            }).then(({
                data
            }) => {
                this.dataList = data.page
            })
        },
        outschoole(id) {
            this.innerVisible = true
            this.$nextTick(() => {
                this.$http({
                    url: this.$http.adornUrl('/tigger/outschoole/list'),
                    method: 'get',
                    params: this.$http.adornParams({
                        cid: id
                    })
                }).then(({
                    data
                }) => {
                    console.log(data)
                    if (data.code == 0) {
                        this.innerdatalist = data.page
                    }
                })
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
