<!--  -->
<template>
<div class=''>
    <el-dialog :title="!dataForm.id?'新增':'修改'" :visible.sync="dialogVisible" width="40%" :close-on-click-modal="false">
        <el-form :model="dataForm" label-width="100px" ref="dataForm" :rules="rules">
            <el-form-item label="课程名" prop="courseName">
                <el-input v-model="dataForm.courseName"></el-input>
            </el-form-item>
            <el-form-item label="课程介绍" prop="introduce">
                <el-input type="textarea" v-model="dataForm.introduce"></el-input>
            </el-form-item>
            <el-form-item label="课程分类" prop="cids">
                <el-cascader :options="category" :show-all-levels="false" :props="defaultProps" v-model="dataForm.cids"></el-cascader>
            </el-form-item>
            <el-form-item label="机构" prop="oid">
                <el-select v-model="dataForm.oid" placeholder="请选择">
                    <el-option v-for="o in organ" :key="o.id" :value="o.id" :label="o.username"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="课程单价" prop="coursePrice">

                <el-input-number v-model="dataForm.coursePrice" :min="0" :precision="2" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="使用积分" prop="coursePoint">
                <el-input-number v-model="dataForm.coursePoint" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="课程图片" prop="image">
                <el-upload action="#" class="upload-demo" list-type="picture" :limit="1" :http-request="uploadimage" :file-list="fileList" :on-remove="removeImage">
                    <el-button size="small" type="primary">点击上传</el-button>
                    <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
                </el-upload>
            </el-form-item>
            <el-form-item label="试看视频" prop="tryvideo">
                <el-select v-model="dataForm.tryvideo" placeholder="请选择">
                    <el-option v-for="t in tryvideo" :key="t.id" :value="t.id" :label="t.videoName"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="是否主页显示">
                <el-switch active-color="#13ce66" inactive-color="#ff4949" :active-value="1" :inactive-value="0" v-model="dataForm.homeShow" active-text="是" inactive-text="否"></el-switch>
            </el-form-item>
            <el-form-item label="是否推荐">
                <el-switch active-color="#13ce66" inactive-color="#ff4949" :active-value="1" :inactive-value="0" v-model="dataForm.recommend" active-text="是" inactive-text="否"></el-switch>
            </el-form-item>
            <el-form-item label="分享得积分">
                <el-input-number :min="0" label="描述文字" v-model="dataForm.sharePoint"></el-input-number>
            </el-form-item>
            <el-form-item label="购买商品得积分">
                <el-input-number :min="0" label="描述文字" v-model="dataForm.byPoint"></el-input-number>
            </el-form-item>
            <el-form-item label="购买商品父级得积分">
                <el-input-number :min="0" label="描述文字" v-model="dataForm.byPointFather"></el-input-number>
            </el-form-item>

        </el-form>
        <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="subimt('dataForm')" v-loading.fullscreen.lock="fullscreenLoading">确定</el-button>
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
            fullscreenLoading: false,
            fileList: [],
            dialogVisible: false,
            dataForm: {
                id: 0,
                courseName: '',
                cids: [],
                oid: "",
                introduce: "",
                coursePrice: "",
                coursePoint: "",
                image: "",
                tryvideo: "",
                homeShow: 0,
                recommend: 0,
                showStatus: 1,
                sharePoint: 0,
                byPoint: 0,
                byPointFather: 0
            },
            category: [],
            defaultProps: {
                "children": "chidrens",
                "label": "name",
                'value': "id",
            },
            rules: {
                courseName: [{
                    required: true,
                    message: '课程名称',
                    trigger: 'blur'
                }],
                cids: [{
                    required: true,
                    message: '请选择分类',
                    trigger: 'blur'
                }],
                oid: [{
                    required: true,
                    message: '请选择机构',
                    trigger: 'blur'
                }],
                introduce: [{
                    required: true,
                    message: '请输入描述',
                    trigger: 'blur'
                }],
                coursePrice: [{
                    required: true,
                    message: '请输入单价',
                    trigger: 'blur'
                }],
                coursePoint: [{
                    required: true,
                    message: '请输入积分',
                    trigger: 'blur'
                }],
                image: [{
                    required: true,
                    message: '请选择图片',
                    trigger: 'blur'
                }],

            },
            organ: [],
            tryvideo: [],

        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        //提交表单
        subimt(formName) {
            this.$refs[formName].validate((vali) => {
                if (vali) {

                    this.dataForm.id = this.dataForm.id == 0 ? undefined : this.dataForm.id

                    this.$http({
                        url: this.$http.adornUrl(`/tigger/course/${this.dataForm.id == undefined?'insertCourse':'editCourse'}`),
                        method: 'post',
                        data: this.$http.adornData(this.dataForm)
                    }).then(({
                        data
                    }) => {
                        if (data.code === 0) {
                            this.dialogVisible = false
                            this.$emit("renewal")
                        }
                    });
                }
            })
        },
        //查询所有的机构
        selectOrgan() {
            this.$http({
                url: this.$http.adornUrl("/tigger/organ/getOrgan"),
                method: "get"
            }).then(({
                data
            }) => {
                if (data.code == 0) {
                    this.organ = data.page
                }
            })
        },
        init(id) {
            this.getCategoryList()
            this.selectOrgan()
            this.getTryVideo()
            //console.log(id)
            this.dataForm.id = id || 0
            this.dialogVisible = true
            this.$nextTick(() => {
                this.$refs['dataForm'].resetFields()

                this.fileList = []
                this.dataForm.image = ""
                this.dataForm.tryvideo = ""
                this.dataForm.homeShow = 0
                this.dataForm.recommend = 0
                if (this.dataForm.id) {
                    this.$http({
                        url: this.$http.adornUrl(`/tigger/course/getCourseId/${this.dataForm.id}`),
                        method: 'get',
                        // params: this.$http.adornParams({})
                    }).then(({
                        data
                    }) => {
                        console.log(data)
                        if (data.code === 0) {
                            this.dataForm.cids = data.page.cids
                            this.dataForm.courseName = data.page.courseName
                            this.dataForm.coursePoint = data.page.coursePoint
                            this.dataForm.coursePrice = data.page.coursePrice
                            this.dataForm.homeShow = data.page.homeShow
                            this.dataForm.image = data.page.image
                            this.dataForm.introduce = data.page.introduce
                            this.dataForm.oid = data.page.oid
                            this.dataForm.recommend = data.page.recommend
                            this.dataForm.tryvideo = data.page.tryvideo

                        }
                    })
                }
            })
        },
        getCategoryList() {
            this.$http({
                url: this.$http.adornUrl("/tigger/category/list"),
                method: "get",
                params: this.$http.adornParams({
                    type: 1
                })
            }).then(({
                data
            }) => {
                if (data.code == 0) {
                    this.category = this.dipose(data.page)

                }
            })
        },
        //处理category数据
        dipose(data) {
            for (var i = 0; i < data.length; i++) {
                if (data[i].chidrens.length == 0) {
                    delete data[i].chidrens;
                } else {
                    this.dipose(data[i].chidrens)
                }
            }
            return data

        },
        removeImage(file) {
            console.log(file)
            this.$http({
                url: this.$http.adornUrl("/tigger/accessory/deletedImage"),
                method: "post",
                data: this.$http.adornData(file.id, false)
            }).then(({
                data
            }) => {
                console.log(data)
                this.fileList = []
            })
        },
        uploadimage(file) {
            //console.log(file)
            let formData = new FormData()
            formData.append("file", file.file)
            this.$http({
                url: this.$http.adornUrl("/tigger/accessory/uploadImage"),
                method: "post",
                data: formData
            }).then(({
                data
            }) => {
                if (data.code === 0) {
                    if (data.page.length > 0) {
                        this.fileList.push({
                            'url': data.page[0].coverSrc,
                            'name': data.page[0].fileName,
                            "id": data.page[0].id,
                        })
                        this.dataForm.image = data.page[0].id
                    }
                }
            })
        },
        getTryVideo() {
            this.$http({
                url: this.$http.adornUrl('/tigger/tryvideo/getTryvideoAll'),
                method: 'get',

            }).then(({
                data
            }) => {
                if (data.code === 0) {
                    this.tryvideo = data.page
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
    activated() {

    }, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>
