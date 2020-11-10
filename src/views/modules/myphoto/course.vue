<!--  -->
<template>
<div class=''>
    <div>
        <el-button type="success" @click="dislogclick()">新增</el-button>
        <el-button type="danger" @click="deleted">删除</el-button>
    </div>
    <br>
    <el-table :data="dataList" border @selection-change="change">
        <el-table-column type="selection" width="55" align="center">
        </el-table-column>
        <el-table-column type="index" label="序号" align="center"></el-table-column>
        <el-table-column label="课程名" align="center" prop="courseName"></el-table-column>
        <el-table-column label="课程介绍" align="center" prop="introduce"></el-table-column>
        <el-table-column label="课程分类" align="center" prop="name">
            <template slot-scope="scope">
                <el-tag type="success" v-for="n in scope.row.name" :key="n">{{n}}</el-tag>
            </template>
        </el-table-column>
        <el-table-column label="机构" align="center" prop="username"></el-table-column>
        <el-table-column label="单价" align="center" prop="coursePrice"></el-table-column>
        <el-table-column label="使用积分" align="center" prop="coursePoint"></el-table-column>
        <el-table-column label="试看视频" align="center" prop="videoName"></el-table-column>
        <el-table-column label="课程图片" align="center" prop="coursePoint">
            <template slot-scope="scope">
                <el-image :src="scope.row.asrc"></el-image>
            </template>

        </el-table-column>
        <el-table-column label="主页是否显示" align="center" prop="homeShow">
            <template slot-scope="scope">
                {{scope.row.homeShow==0?'否':'是'}}
            </template>
        </el-table-column>
        <el-table-column label="是否推荐" align="center" prop="recommend">
            <template slot-scope="scope">
                {{scope.row.recommend==0?'否':'是'}}
            </template>
        </el-table-column>
        <el-table-column label="分享得积分" align="center" prop="sharePoint"></el-table-column>
        <el-table-column label="购买得积分" align="center" prop="byPoint"></el-table-column>
        <el-table-column label="购买父得积分" align="center" prop="byPointFather"></el-table-column>
        <el-table-column label="操作" align="center">
            <template slot-scope="scope">
                <el-button type="text" @click="dislogclick(scope.row.id)">修改</el-button>
            </template>
        </el-table-column>

    </el-table>
    <add-or-update ref="addOrUpdate" v-if="dislogfalg" @renewal="selectCourse()"></add-or-update>
</div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';
import AddOrUpdate from './course-add-or-update'
export default {
    //import引入的组件需要注入到对象中才能使用
    components: {
        AddOrUpdate
    },
    data() {
        //这里存放数据
        return {
            dislogfalg: false,
            dataList: [],
            pageIndex: 1,
            pageSize: 10,
            totalPage: 0,
            selectList: []
        };
    },
    //监听属性 类似于data概念
    computed: {

    },
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        //新增或修改
        dislogclick(id) {
            this.dislogfalg = true
            this.$nextTick(() => {
                this.$refs.addOrUpdate.init(id)
            })
        },
        selectCourse() {
            this.$http({
                url: this.$http.adornUrl('/tigger/course/selectCourse'),
                method: 'get',
                params: this.$http.adornParams({
                    page: this.pageIndex,
                    limit: this.pageSize,
                })
            }).then(({
                data
            }) => {
                console.log(data)
                if (data.code === 0) {
                    this.dataList = data.page.list
                    this.totalPage = data.page.totalCount
                } else {
                    this.dataList = []
                    this.totalPage = 0
                }
            })
        },
        change(val) {
            //console.log(val)
            this.selectList = val
        },
        deleted() {
            this.$confirm("此操作将永久删除该文件, 是否继续?", '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                let ids = []
                this.selectList.map(item => {
                    ids.push(item.id)
                })
                this.$http({
                    url: this.$http.adornUrl('/tigger/course/deletedCourse'),
                    method: 'post',

                    data: this.$http.adornData(ids, false)
                }).then(({
                    data
                }) => {
                    if (data.code === 0) {
                        this.$message({
                            message: "删除成功",
                            type: "success"
                        })
                        this.selectCourse()
                    }
                });
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
        this.selectCourse()
    }, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>
