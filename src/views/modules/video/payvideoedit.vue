<!--  -->
<template>
<div class=''>
    <el-dialog title="修改收费视频" :visible.sync="dialogVisible" width="30%">
        <el-form label-width="80px" ref="video" :model="video" :rules="rules">
            <el-form-item label="视频描述" prop="introduce">
                <el-input v-model="video.introduce" placeholder="请输入视频描述"></el-input>
            </el-form-item>
            <el-form-item label="视频分类" prop="cid">
                <el-cascader :options="category" :show-all-levels="false" :props="defaultProps" v-model="video.cid"></el-cascader>
            </el-form-item>
            <el-form-item label="机构" prop="oid">
                <el-select v-model="video.oid" placeholder="请选择">
                    <el-option v-for="o in organ" :key="o.id" :value="o.id" :label="o.username"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="价格" prop="price">
                <el-input type="number" v-model="video.price" style="width:30%"></el-input>
            </el-form-item>
            <el-form-item label="封面" prop="fileList">
                <el-upload list-type="picture-card" accept=".png,.jpg" :auto-upload="false" :multiple="false" :limit="1" ref="upload1" action="#" :on-change="onChangeImage">
                </el-upload>
            </el-form-item>

            <el-form-item label="视频" prop="fileList">

                <el-upload action="#" list-type="text" accept=".mp4" :limit="1" :auto-upload="false" :on-change="onChangeVideo" :on-remove="onRemoveVideo" ref="upload">
                    <el-button type="primary">选择视频</el-button>
                </el-upload>
            </el-form-item>

            <el-form-item label="使用积分" prop="point">
                <el-input type="number" v-model="video.point" style="width:30%"></el-input>
            </el-form-item>

            <el-form-item label="主页显示" prop="homeShow">
                <el-switch v-model="video.homeShow" active-color="#13ce66" inactive-color="#ff4949" active-text="是" inactive-text="否">
                </el-switch>
            </el-form-item>
        </el-form>
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
            rules: {
                introduce: [{
                    required: true,
                    message: '请输入视频描述',
                    trigger: 'blur'
                }],
                price: [{
                    required: true,
                    message: '请输入价格',
                    trigger: 'blur'
                }],
                cid: [{
                    required: true,
                    message: '请选择分类',
                    trigger: 'change'
                }],
                fileList: [{
                    required: true,
                    message: '请选择图片或视频',
                    trigger: 'change'
                }],
                point: [{
                    required: true,
                    message: '请输入积分',
                    trigger: 'blur'
                }]
            },
        };
    },
    props: {
        video: Object
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {
        video(val) {
            this.dialogVisible = true
        }
    },
    //方法集合
    methods: {

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
