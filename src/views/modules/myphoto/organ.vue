<!--  -->
<template>
<div class=''>
    <div>
        <el-button type="success" round @click="dialogFormVisible = true">添加</el-button>
        <el-button type="danger" round @click="deletedOrgan">删除</el-button>
    </div>
    <br />
    <div>
        <el-table :data="dataList" border @selection-change="selectchange">
            <el-table-column type="selection" width="55" align="center"></el-table-column>
            <el-table-column type="index" label="序号" align="center"> </el-table-column>
            <el-table-column label="登录名" prop="loginname" align="center"></el-table-column>
            <el-table-column label="分类">
                <template slot-scope="scope">
                    <el-tag type="success" v-for=" n in scope.row.name" :key="n">{{n}}</el-tag>
                </template>
            </el-table-column>
            <el-table-column label="机构名" prop="username" align="center"></el-table-column>
            <el-table-column label="图标" align="center">
                <template slot-scope="scope">
                    <div>
                        <el-image style="width: 100px; height: 100px" :src="scope.row.image" :fit="fit"></el-image>
                    </div>
                </template>
            </el-table-column>
            <el-table-column label="简介" prop="synopsis" align="center"></el-table-column>
            <el-table-column label="是否主页显示" prop="homeShow" align="center">
                <template slot-scope="scope">
                    {{scope.row.homeShow==1?'是':'否'}}
                </template>
            </el-table-column>
            <el-table-column label="是否推荐" prop="recommend" align="center">
                <template slot-scope="scope">
                    {{scope.row.recommend==1?'是':'否'}}
                </template>
            </el-table-column>
            <el-table-column label="操作" align="center">
                <template slot-scope="scope">
                    <el-button type="text" @click="dialogFormVisible=true,edit(scope.row)">修改</el-button>
                    <el-button type="text" @click="dialogTable=true,selectStudent(scope.row)">查看学员</el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination small layout="prev, pager, next" :total="totalPage" @current-change="currentChange" @size-change="sizeChange" :page-size="pageSize" :page-count="totalPage" :current-page="pageIndex">
        </el-pagination>
    </div>
    <el-dialog title="机构" :visible.sync="dialogFormVisible" width="40%" :close-on-click-modal="false">
        <div slot="footer" class="dialog-footer">
            <el-form :model="myorgan" label-width="100px" status-icon :rules="rules" ref="myorgan">
                <el-form-item label="机构名:" prop="username">
                    <el-input v-model="myorgan.username"></el-input>
                </el-form-item>
                <el-form-item label="分类:" prop="cids">
                    <el-cascader :options="category" :show-all-levels="false" :props="defaultProps" v-model="myorgan.cids"></el-cascader>
                </el-form-item>
                <el-form-item label="登录名:" prop="loginname">
                    <el-input v-model="myorgan.loginname"></el-input>
                </el-form-item>
                <el-form-item label="简介：" prop="synopsis">
                    <el-input type="textarea" v-model="myorgan.synopsis"></el-input>
                </el-form-item>
                <el-form-item label="密码:" prop="password">
                    <el-input type="password" show-password size="small" v-model="myorgan.password"></el-input>
                </el-form-item>
                <el-form-item label="确定密码:" prop="checkpassword">
                    <el-input type="password" show-password size="small" v-model="myorgan.checkpassword"></el-input>
                </el-form-item>
                <el-form-item label="图标：" prop="icon">
                    <el-upload action="#" class="upload-demo" list-type="picture" :limit="1" :http-request="uploadimage" :file-list="fileList" :on-remove="removeImage">
                        <el-button size="small" type="primary">点击上传</el-button>
                        <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
                    </el-upload>
                </el-form-item>
                <el-form-item label="是否主页显示">
                    <el-switch active-color="#13ce66" inactive-color="#ff4949" :active-value="1" :inactive-value="0" v-model="myorgan.homeShow" active-text="是" inactive-text="否"></el-switch>
                </el-form-item>
                <el-form-item label="是否推荐">
                    <el-switch active-color="#13ce66" inactive-color="#ff4949" :active-value="1" :inactive-value="0" v-model="myorgan.recommend" active-text="是" inactive-text="否"></el-switch>
                </el-form-item>
            </el-form>
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button v-if="myorgan.id == null" type="primary" @click="submitForm('myorgan')">确 定</el-button>
            <el-button v-if="myorgan.id != null" type="primary" @click="editOrgan">确 定</el-button>
        </div>
    </el-dialog>

    <el-dialog :visible.sync="dialogTable">
        <el-table border :data="dialoglist">
            <el-table-column align="center" type="index" label="序号"></el-table-column>
            <el-table-column align="center" prop="psUsername" label="学生名"></el-table-column>
            <el-table-column align="center" prop="iphone" label="电话"></el-table-column>
            <el-table-column align="center" prop="username" label="机构"></el-table-column>
            <el-table-column align="center" label="学生文档">
                <template slot-scope="scope">
                    <el-link type="primary" @click="lookwendang(scope.row)">文档</el-link>
                </template>
            </el-table-column>
            <el-table-column>
                <template slot-scope="scope">
                    <el-button type="primary" @click="innerVisible2=true,content=scope.row.message,student=scope.row">编辑文档</el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-dialog :visible.sync="innerVisible3" append-to-body>
            <div id="content">

            </div>
        </el-dialog>

        <el-dialog :visible.sync="innerVisible2" append-to-body>
            <quill-editor ref="text" v-model="content" clacss="myQuillEditor" :options="editorOption" />
            <el-button type="primary" @click="updateOrganMessage" style="margin:right">确定</el-button>
        </el-dialog>

    </el-dialog>
</div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';
import {
    quillEditor
} from 'vue-quill-editor'
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
export default {
    //import引入的组件需要注入到对象中才能使用
    components: {
        quillEditor
    },
    data() {
        var validatePass = (rule, value, callback) => {
            if (value == '') {
                callback(new Error('请再次输入密码'))
            } else if (value != this.myorgan.password) {
                callback(new Error('两次输入密码不一致!'));
            } else {
                callback()
            }
        }
        //这里存放数据
        return {
            fit: 'scale-down',
            fileList: [],
            innerVisible2: false,
            dialoglist: [],
            innerVisible3: false,
            dialogFormVisible: false,
            dialogTable: false,
            content: '',
            student: {},
            editorOption: {},
            myorgan: {
                id: null,
                username: "",
                password: "",
                loginname: "",
                cids: [],
                checkpassword: "",
                synopsis: "",
                icon: "#icon-books",
                imageId: "",
                homeShow: 0,
                recommend: 0
            },
            //icons: ["#icon-books", "#icon-operation", "#icon-doctor", "#icon-ruler", "#icon-find", "#icon-notebook", "#icon-hat", "#icon-palette", "#icon-computer", "#icon-books1", "#icon-link", "#icon-keyboard", "#icon-men", "#icon-hart", "#icon-study", "#icon-book", "#icon-globe", "#icon-running", "#icon-experiment", "#icon-cube"],
            innerVisible: false,
            rules: {
                synopsis: [{
                    required: false,
                    message: '请输入活动名称',
                    trigger: 'blur'
                }],
                icon: [{
                    required: false,
                    message: '请输入活动名称',
                    trigger: 'blur'
                }],
                username: [{
                    required: true,
                    message: '请输入活动名称',
                    trigger: 'blur'
                }, ],
                password: [{
                    required: true,
                    message: '请输入密码',
                    trigger: 'blur',
                }, {
                    min: 6,
                    max: 16,
                    message: '密码长度在 6 到 16 ',
                    trigger: 'blur'
                }],
                checkpassword: [{
                    validator: validatePass,
                    trigger: 'blur'
                }],
                loginname: [{
                    required: true,
                    message: '请输入活动名称',
                    trigger: 'blur'
                }],
                cids: [{
                    required: true,
                    message: '请选择分类名称',
                    trigger: 'blur'
                }]
            },
            pageIndex: 0,
            pageSize: 10,
            totalPage: 0,
            dataList: [],
            organselect: [],
            row: null,
            category: [],
            defaultProps: {
                "children": "chidrens",
                "label": "name",
                'value': "id",
            },
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {
        dialogFormVisible() {

            this.fileList = []
        }
    },
    //方法集合
    methods: {
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
                console.log(data)
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
                        this.myorgan.imageId = data.page[0].id
                    }
                }
            })
        },
        lookwendang(val) {
            //console.log(val.message)
            setTimeout(function () {
                //console.log(val.message)
                document.getElementById("content").innerHTML = val.message
            }, 1000);
            this.innerVisible3 = true

        },
        editOrgan() {
            this.$http({
                url: this.$http.adornUrl("/tigger/organ/editOrgan"),
                method: "post",
                data: this.$http.adornData(this.myorgan)
            }).then(({
                data
            }) => {
                this.getAllOrgan()
                this.dialogFormVisible = false
                if (data.code == 0) {
                    this.$message({
                        message: "修改成功",
                        type: "success"
                    })
                    this.$refs['myorgan'].resetFields();
                    this.$refs['myorgan'].clearValidate();

                } else {
                    this.$message({
                        message: "修改失败",
                        type: "warning"
                    })
                }
                this.myorgan.id = null
                this.myorgan.checkpassword = ''
            })
        },
        edit(val) {
            console.log(val)
            this.myorgan.id = val.id
            this.myorgan.icon = val.icon
            this.myorgan.loginname = val.loginname
            this.myorgan.synopsis = val.synopsis
            this.myorgan.username = val.username

            if (val.cids != null) {

                this.myorgan.cids = val.cids
            }
            this.myorgan.recommend = val.recommend
            this.myorgan.homeShow = val.homeShow
        },
        selectchange(val) {
            // console.log(val)

            this.organselect = val
        },

        //删除
        deletedOrgan() {
            let list = []
            this.organselect.map(item => {
                list.push(item.id)
            })
            this.$confirm("所选项是否删除？", '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.$http({
                    url: this.$http.adornUrl("/tigger/organ/deleteOrgan"),
                    method: "post",
                    data: this.$http.adornData(list, false)
                }).then(({
                    data
                }) => {
                    this.getAllOrgan()
                    if (data.code == 0) {

                        this.$message({
                            message: "删除成功",
                            type: "success"
                        })
                    } else {
                        this.$message({
                            message: data.message,
                            type: "warning"
                        })
                    }
                })
            })
        },
        selectStudent(row) {
            this.row = row
            this.$http({
                url: this.$http.adornUrl("/tigger/organ/getOrganStudent"),
                method: "get",
                params: this.$http.adornParams({
                    id: row.id
                })
            }).then(({
                data
            }) => {
                //console.log(data)
                if (data.code == 0) {
                    this.dialoglist = data.page
                }
            })
        },
        currentChange(val) {
            this.pageIndex = val;
            this.getAllOrgan()
        },
        //点击页数
        sizeChange(val) {
            this.pageSize = val
            this.getAllOrgan()
        },

        submitForm(formName) {

            this.$refs[formName].validate((valid) => {
                if (valid) {
                    //alert('submit!');
                    this.insertOrgan()
                } else {
                    // console.log('error submit!!');
                    return false;
                }
            });
        },
        insertOrgan() {
            this.myorgan.cids.toString()
            this.$http({
                url: this.$http.adornUrl("/tigger/organ/insertOrgan"),
                method: "post",
                data: this.$http.adornData(this.myorgan)
            }).then(({
                data
            }) => {
                if (data.code == 0) {
                    this.dialogFormVisible = false

                    this.$refs['myorgan'].resetFields();
                }
                this.getAllOrgan()
                //console.log(data)
            })
        },
        //获取所有的数据
        getAllOrgan() {
            this.$http({
                url: this.$http.adornUrl("/tigger/organ/getAllOrgan"),
                method: "get",
                params: this.$http.adornParams({
                    page: this.pageIndex,
                    limit: this.pageSize,
                })
            }).then(({
                data
            }) => {

                if (data.code == 0) {

                    this.totalPage = data.page.totalCount
                    this.dataList = data.page.list
                } else {
                    this.totalPage = 0
                }
            })
        },
        updateOrganMessage() {
            this.$http({
                url: this.$http.adornUrl("/tigger/record/updateOrganMessage"),
                method: "post",
                data: this.$http.adornData({
                    student: this.student,
                    message: this.content
                })
            }).then(({
                data
            }) => {
                this.innerVisible2 = false
                this.selectStudent(this.row)
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
        this.getCategoryList()
        this.getAllOrgan()
    }, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>

<style scoped>
.icon {
    width: 4em;
    height: 4em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
    float: left;
}
</style>
