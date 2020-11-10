<!--  -->
<template>
<div class=''>
    <el-dialog title="添加商品" :visible.sync="dialogVisible" width="40%" @close="editclose">
        <el-form ref="product" :model="product" :rules="rules" label-width="100px">
            <el-form-item label="商品名称：" prop="comName">
                <el-input style="width:40%" v-model="product.comName"></el-input>
            </el-form-item>
            <el-form-item label="分类：" prop="cid">

                <el-cascader :options="category" :show-all-levels="false" :props="defaultProps" v-model="product.cid"></el-cascader>
            </el-form-item>
            <el-form-item label="积分：  " prop="point">
                <el-input-number v-model="product.point" :min="0" :step="0.1" :precision="2"></el-input-number>
            </el-form-item>

            <el-form-item label="价格：  " prop="price">
                <el-input-number v-model="product.price" :min="0" :step="0.1" :precision="2"></el-input-number>
            </el-form-item>
            <el-form-item label="邮费：" prop="postage">
                <el-input-number v-model="product.postage" :min="0" :step="0.1" :precision="2"></el-input-number>
            </el-form-item>
            <el-form-item label="轮播图：" prop="fileList">
                <el-upload action="#" list-type="picture-card" ref="upload" :auto-upload="false" :multiple="true" :on-change="imageChange" accept=".png,.jpg" :on-remove="imageRemove">
                    <i slot="default" class="el-icon-plus"></i>
                </el-upload>
            </el-form-item>
            <el-form-item label="封面" prop="cover">
                <el-upload action="#" list-type="picture-card" ref="upload1" :auto-upload="false" :limit="1" :on-change="coverChange" accept=".png,.jpg">
                    <i slot="default" class="el-icon-plus"></i>
                </el-upload>
            </el-form-item>
            <el-form-item label="主页显示：">
                <el-switch v-model="product.homeShow" active-color="#13ce66" inactive-color="#ff4949" active-text="是" inactive-text="否" :active-value="1" :inactive-value="0">
                </el-switch>
            </el-form-item>
            <el-form-item label="分享得积分">
                <el-input-number :min="0" label="描述文字" v-model="product.sharePoint"></el-input-number>
            </el-form-item>
            <el-form-item label="购买商品得积分">
                <el-input-number :min="0" label="描述文字" v-model="product.byPoint"></el-input-number>
            </el-form-item>
            <el-form-item label="购买商品父级得积分">
                <el-input-number :min="0" label="描述文字" v-model="product.byPointFather"></el-input-number>
            </el-form-item>
            <el-form-item label="商家地址">
                <el-input label="描述文字" v-model="product.address"></el-input>
            </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer" v-if="edit">
            <el-button @click="dialogTableVisible = false">取 消</el-button>
            <el-button type="primary" @click="editproduct" v-loading.fullscreen.lock="fullscreenLoading">修改</el-button>
        </span>
        <span slot="footer" class="dialog-footer" v-if="!edit">
            <el-button @click="dialogTableVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitForm('product')" v-loading.fullscreen.lock="fullscreenLoading">确 定</el-button>
        </span>
    </el-dialog>
    <div>
        <el-button type="primary" round @click="dialogVisible =true">添加商品</el-button>
        <el-button type="danger" round @click="deletedProduct">删除</el-button>
    </div>
    <br>
    <div>
        <el-table :data="dataList" border @selection-change="selectChange">
            <el-table-column type="selection" width="55">
            </el-table-column>
            <el-table-column label="序号" type="index">
            </el-table-column>
            <el-table-column label="商品名称" prop="comName" align="center">
            </el-table-column>
            <el-table-column label="分类">
                <template slot-scope="scope">
                    <el-tag v-for="tag in scope.row.name" :key="tag" :disable-transitions="false">{{tag}}</el-tag>
                </template>
            </el-table-column>

            <el-table-column label="积分" prop="point" align="center"></el-table-column>
            <el-table-column label="金额" prop="price" align="center"></el-table-column>
            <el-table-column label="邮费" prop="postage" align="center">
                <template slot-scope="scope">
                    <span>
                        {{scope.row.postage==0?"包邮":scope.row.postage}}
                    </span>
                </template>
            </el-table-column>
            <el-table-column label="轮播图" prop="asrc" align="center">
                <template slot-scope="scope">
                    <el-image style="width: 100px; height: 100px" :src="scope.row.asrc" :preview-src-list="scope.row.accessoryEntities"></el-image>
                </template>
            </el-table-column>
            <el-table-column prop="homeShow" label="是否主页显示" align="center">
                <template slot-scope="scope">

                    <el-switch v-model="scope.row.homeShow" :active-value="1" @change="switchChange($event,scope.row)" :inactive-value="0"></el-switch>
                </template>
            </el-table-column>
            <el-table-column label="分享得积分" align="center" prop="sharePoint"></el-table-column>
            <el-table-column label="购买得积分" align="center" prop="byPoint"></el-table-column>
            <el-table-column label="购买父得积分" align="center" prop="byPointFather"></el-table-column>
            <el-table-column label="详情" align="center">
                <template slot-scope="scope">
                    <el-button type="text" @click="productmessagecompentclick(scope.row.id)">详情</el-button>
                </template>
            </el-table-column>
            <el-table-column label="商家地址" align="center" prop="address">

            </el-table-column>
            <el-table-column label="操作" align="center">
                <template slot-scope="scope">
                    <el-button type="text" round @click="check(scope.row)">查看</el-button>
                    <el-button type="text" round @click="editProduct(scope.row)">修改</el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination small layout="prev, pager, next" :total="totalPage" @current-change="currentChange" @size-change="sizeChange" :page-size="pageSize" :page-count="totalPage" :current-page="pageIndex">
        </el-pagination>
    </div>
    <div>
        <el-dialog title="轮播图" :visible.sync="dialogCarousel">
            <el-carousel indicator-position="outside">
                <el-carousel-item v-for="item in carousel" :key="item">
                    <el-image :src="item"></el-image>
                </el-carousel-item>
            </el-carousel>
        </el-dialog>
    </div>
    <productmessage ref="productmessage" v-if="productmessagecompent"></productmessage>
</div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';
import productmessage from './productmessage'
export default {
    //import引入的组件需要注入到对象中才能使用
    components: {
        productmessage
    },
    data() {
        //这里存放数据
        return {
            productmessagecompent: false,
            tableSelectList: [],
            dialogCarousel: false,
            dialogVisible: false,
            product: {
                comName: "",
                cid: [],
                point: "",
                price: "",
                fileList: [],
                cover: null,
                homeShow: 0,
                postage: "",
                sharePoint: 0,
                byPoint: 0,
                byPointFather: 0,
                address: ''
            },
            defaultProps: {
                "children": "chidrens",
                "label": "name",
                'value': "id",

            },

            category: [],
            fullscreenLoading: false,
            rules: {
                comName: [{
                    required: true,
                    message: '商品名称',
                    trigger: 'blur'
                }],
                cid: [{
                    required: true,
                    message: '请选择分类',
                    trigger: 'change'
                }],
                point: [{
                    required: true,
                    message: '请输入积分',
                    trigger: 'blur'
                }],
                price: [{
                    required: true,
                    message: '请输入金额',
                    trigger: 'blur'
                }],
                fileList: [{
                    required: true,
                    message: '请上传文件',
                    trigger: 'change'
                }]

            },
            dataList: [],
            pageIndex: 1,
            pageSize: 10,
            totalPage: 0,
            carousel: [],
            edit: false
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        productmessagecompentclick(id) {
            this.productmessagecompent = true
            this.$nextTick(() => {
                this.$refs.productmessage.getdata(id)
            })
        },
        //点击页
        currentChange(val) {
            this.pageIndex = val;
            this.getPayVideo()
        },
        //点击页数
        sizeChange(val) {
            this.pageSize = val
            this.getPayVideo()
        },
        switchChange($event, row) {
            this.$http({
                url: this.$http.adornUrl("/tigger/comment/updateShowStatus"),
                method: "post",
                data: this.$http.adornData({
                    id: row.id,
                    status: $event
                })
            }).then(({
                data
            }) => {
                console.log(data)
                if (data.code != 0) {
                    this.$message({
                        message: "更新失败",
                        type: "waring"
                    })
                }
            })
        },
        editproduct() {
            this.fullscreenLoading = true
            let formData = new FormData()

            if (this.product.fileList != null) {
                this.product.fileList.forEach(item => {
                    formData.append("file", item.raw)
                })
            }
            console.log(this.product)
            if (this.product.cover != null) {

                formData.set("cover", this.product.cover)
            }
            formData.set("comName", this.product.comName)
            formData.set("cid", this.product.cid)
            formData.set("point", parseFloat(this.product.point))
            formData.set("price", parseFloat(this.product.price))
            formData.set("homeShow", this.product.homeShow)
            formData.set("postage", parseFloat(this.product.postage))
            formData.set("id", this.product.id)
            formData.set("sharePoint", this.product.sharePoint)
            formData.set("byPoint", this.product.byPoint)
            formData.set("byPointFather", this.product.byPointFather)
            formData.set("address", this.product.address)
            this.$http({
                url: this.$http.adornUrl("/tigger/comment/insertProduct"),
                method: "post",
                data: formData,
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded;'
                },
            }).then(({
                data
            }) => {
                this.fullscreenLoading = false
                this.dialogVisible = false
                if (data.code == 0) {
                    this.$message({
                        message: "修改成功",
                        type: "success"
                    })
                    this.getProduct()
                    this.product.fileList = []
                    this.$refs["product"].resetFields()
                    this.$refs["upload"].clearFiles()
                    this.$refs["upload1"].clearFiles()
                }
            })
        },
        deletedProduct() {
            if (this.tableSelectList.length == 0) {
                this.$message({
                    message: '请选择要删除的选项！',
                    type: "warning"
                })
                return;
            }
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.$http({
                    url: this.$http.adornUrl("/tigger/comment/product"),
                    method: "post",
                    data: this.$http.adornData({
                        "product": this.tableSelectList
                    })
                }).then(({
                    data
                }) => {
                    if (data.code == 0) {
                        this.getProduct()
                    }
                })
            })
            console.log(this.tableSelectList)

        },

        //selectChange
        //点击多选的时候
        selectChange(val) {
            this.tableSelectList = val
        },
        editProduct(row) {
            console.log(row)
            if (typeof (row.cid) == 'string') {
                row.cid = row.cid.replace(RegExp(" ", "g"), "").replace(RegExp("\\[", "g"), "").replace(RegExp("\\]", "g"), "").split(',')
                for (var i = 0; i < row.cid.length; i++) {
                    row.cid[i] = parseInt(row.cid[i])
                }
                console.log(row)

            }
            row.cover = null
            this.product = row
            this.dialogVisible = true
            this.edit = true
        },
        //图片点击
        check(row) {
            //console.log(11)
            this.dialogCarousel = true
            this.carousel = row.accessoryEntities
        },
        //获取所有的分类信息
        getCategoryList() {
            this.$http({
                url: this.$http.adornUrl("/tigger/category/list"),
                method: "get",
                params: this.$http.adornParams({
                    type: 2
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
        dipose(data) {

            for (var i = 0; i < data.length; i++) {
                if (data[i].chidrens.length == 0) {
                    delete data[i].chidrens;
                } else {
                    this.dipose(data[i].chidrens)
                }
            }
            console.log(data)
            return data

        },
        //表单验证
        submitForm(formName) {
            this.$refs[formName].validate(val => {
                if (val) {
                    this.insertProduct()
                } else {
                    return false;
                }
            })
        },
        //提交表单
        insertProduct() {
            this.fullscreenLoading = true
            let formData = new FormData()
            this.product.fileList.forEach(item => {
                formData.append("file", item.raw)
            })
            formData.set("comName", this.product.comName)
            formData.set("cid", this.product.cid)
            formData.set("point", this.product.point)
            formData.set("price", this.product.price)
            formData.set("cover", this.product.cover)
            formData.set("homeShow", this.product.homeShow)
            formData.set("postage", this.product.postage)
            formData.set("id", 0)
            formData.set("sharePoint", this.product.sharePoint)
            formData.set("byPoint", this.product.byPoint)
            formData.set("byPointFather", this.product.byPointFather)
            formData.set("address", this.product.address)
            this.$http({
                url: this.$http.adornUrl("/tigger/comment/insertProduct"),
                method: "post",
                data: formData
            }).then(({
                data
            }) => {
                this.fullscreenLoading = false
                this.dialogVisible = false
                if (data.code == 0) {
                    this.$message({
                        message: "上传成功",
                        type: "success"
                    })
                    this.getProduct()
                    this.product.fileList = []
                    this.$refs["product"].resetFields()
                    this.$refs["upload"].clearFiles()
                    this.$refs["upload1"].clearFiles()
                }
            })

        },
        //图片改变
        imageChange(file, fileList) {
            console.log(fileList);

            this.product.fileList = fileList

        },
        imageRemove(file, fileList) {
            this.product.fileList = fileList
        },
        //封面改变(){}
        coverChange(file, fileList) {
            if (file.status == "ready") {
                this.product.cover = file.raw
            }
        },
        //获取所有的商品
        getProduct() {
            this.$http({
                url: this.$http.adornUrl("/tigger/comment/list"),
                method: "get",
                params: this.$http.adornParams({
                    page: this.pageIndex,
                    limit: this.pageSize,
                })
            }).then(({
                data
            }) => {
                //console.log(data)
                if (data.code == 0) {
                    this.dataList = data.page.list
                    this.totalPage = data.page.totalCount
                } else {
                    this.dataList = []
                    this.totalPage = 0
                }
            })
        },
        editclose() {
            Object.assign(this.product, this.$options.data().product)
            this.edit = false
            this.getProduct()
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
        this.getProduct()
    }, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>

<style>
.avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
}

.avatar-uploader .el-upload:hover {
    border-color: #409EFF;
}

.avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
}

.avatar {
    width: 178px;
    height: 178px;
    display: block;
}
</style>
