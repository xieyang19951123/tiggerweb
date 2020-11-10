<!--  -->
<template>
<div class=''>
    <!--视频上传-->
    <div>
        <el-dialog :visible.sync="dialogTableVisible" width="30%" @close="dialogClose2">
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
                    <el-upload list-type="picture-card" accept=".png,.jpg" :on-remove="onRemoveImage" :auto-upload="false" :multiple="false" :limit="1" ref="upload1" action="#" :on-change="onChangeImage">
                    </el-upload>
                </el-form-item>

                <el-form-item label="视频" prop="fileList">

                    <el-upload action="#" list-type="text" accept=".mp4" :on-remove="onRemoveVideo" :limit="1" :auto-upload="false" :on-change="onChangeVideo" ref="upload">
                        <el-button type="primary">选择视频</el-button>
                    </el-upload>
                </el-form-item>

                <el-form-item label="使用积分" prop="point">
                    <el-input type="number" v-model="video.point" style="width:30%"></el-input>
                </el-form-item>

                <el-form-item label="主页显示" prop="homeShow">
                    <el-switch v-model="video.homeShow" active-color="#13ce66" inactive-color="#ff4949" active-text="是" inactive-text="否" :active-value="1" :inactive-value="0">
                    </el-switch>
                </el-form-item>
                <el-form-item label="分享得积分">
                    <el-input-number :min="0" label="描述文字" v-model="video.sharePoint"></el-input-number>
                </el-form-item>
                <el-form-item label="购买商品得积分">
                    <el-input-number :min="0" label="描述文字" v-model="video.byPoint"></el-input-number>
                </el-form-item>
                <el-form-item label="购买商品父级得积分">
                    <el-input-number :min="0" label="描述文字" v-model="video.byPointFather"></el-input-number>
                </el-form-item>
            </el-form>

            <span slot="footer" class="dialog-footer" v-if="!edit">
                <el-button @click="dialogTableVisible = false">取 消</el-button>
                <el-button type="primary" @click="submitForm('video')" v-loading.fullscreen.lock="fullscreenLoading">确 定</el-button>
            </span>
            <span slot="footer" class="dialog-footer" v-if="edit">
                <el-button @click="dialogTableVisible = false">取 消</el-button>
                <el-button type="primary" @click="updateForm('video')" v-loading.fullscreen.lock="fullscreenLoading">修改</el-button>
            </span>
        </el-dialog>

    </div>

    <div>
        <el-button type="success" round @click="dialogTableVisible = true">
            上传视频
        </el-button>
        <el-button type="danger" round @click="deleteTableSelect">
            批量删除
        </el-button>
    </div>
    <br>
    <!--表格-->
    <div>
        <el-table border :data="dataList" style="width: 100%" @selection-change="selectChange">
            <el-table-column type="selection" width="55" align="center">
            </el-table-column>
            <el-table-column type="index" label="序号" align="center"></el-table-column>
            <el-table-column prop="introduce" label="视频描述" align="center">

            </el-table-column>
            <el-table-column label="分类" align="center">
                <template slot-scope="scope">
                    <el-tag v-for="tag in scope.row.name" :key="tag" :disable-transitions="false">{{tag}}</el-tag>
                </template>
            </el-table-column>
            <el-table-column prop="organname" label="机构" align="center"></el-table-column>
            <el-table-column prop="point" label="积分" align="center"></el-table-column>
            <el-table-column prop="price" label="价格" align="center"></el-table-column>
            <el-table-column prop="sales" label="销量" align="center"></el-table-column>
            <el-table-column prop="asrc" label="封面" align="center">
                <template slot-scope="scope">
                    <el-image style="width: 100px; height: 100px ; margin: auto;" :src="scope.row.asrc" :fit="fit"></el-image>
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
            <el-table-column label="操作" align="center">
                <template slot-scope="scope">
                    <el-button type="text" @click="playVideo(scope.row)" size="mini">播放</el-button>
                    <el-button type="text" @click="editplayVideo(scope.row)" size="mini">修改</el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination small layout="prev, pager, next" :total="totalPage" @current-change="currentChange" @size-change="sizeChange" :page-size="pageSize" :page-count="totalPage" :current-page="pageIndex">
        </el-pagination>
    </div>
    <!--视频播放的弹框-->
    <div>
        <el-dialog title="视频播放" :visible.sync="diaLogVideoFalag" @close="dialogClose" @open="dialogOpen">
            <div class="prism-player" id="J_prismPlayer"></div>
        </el-dialog>
    </div>

</div>
</template>

<script>

</script><script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';
import axios from 'axios'
import Vue from 'vue'
import payvideoedit from './payvideoedit'
export default {
    //import引入的组件需要注入到对象中才能使用
    components: {
        payvideoedit
    },
    data() {
        //这里存放数据
        return {
            dialogTableVisible: false,
            diaLogVideoFalag: false,
            category: [],
            innerVisible: false,
            fullscreenLoading: false,
            video: {
                introduce: "",
                price: "",
                cid: [],
                fileList: null,
                point: "",
                homeShow: 0,
                cover: null,
                oid: "",
                sharePoint: 0,
                byPoint: 0,
                byPointFather: 0

            },
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
            dataList: [],
            pageIndex: 1,
            pageSize: 10,
            totalPage: 0,
            fit: 'contain',
            player: null,
            row: null,
            tableSelectList: [],
            defaultProps: {
                "children": "chidrens",
                "label": "name",
                'value': "id",

            },
            organ: [],
            edit: false
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: { //修改视频
        editplayVideo(row) {
            console.log(row)
            if (typeof (row.cid) == 'string') {
                row.cid = row.cid.replace(RegExp(" ", "g"), "").replace(RegExp("\\[", "g"), "").replace(RegExp("\\]", "g"), "").split(',')
                for (var i = 0; i < row.cid.length; i++) {
                    row.cid[i] = parseInt(row.cid[i])
                }
                console.log(row)

            }
            if (row.oid != '' && row.oid != null) {

                row.oid = parseInt(row.oid)
            }
            this.dialogTableVisible = true
            this.video = row
            this.edit = true

        },
        //批量删除
        deleteTableSelect() {
            this.fullscreenLoading = true;
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
                let ids = this.tableSelectList.map((
                    item
                ) => {
                    return item.id;
                })
                let videoIds = this.tableSelectList.map((
                    item
                ) => {
                    return item.videoId;
                })
                // console.log(ids)
                this.$http({
                    url: this.$http.adornUrl("/tigger/payvideo/deletedVideo"),
                    method: "post",
                    data: this.$http.adornData({
                        "ids": ids,
                        "videoIds": videoIds
                    }, false),
                }).then(({
                    data
                }) => {
                    this.fullscreenLoading = false;

                    if (data.code == 0) {
                        this.$message({
                            message: "删除成功",
                            type: "success"
                        })
                        this.getPayVideo()
                    } else {
                        this.$message({
                            message: "删除失败",
                            type: "warning"
                        })
                    }
                })
            })

        },
        //点击多选的时候
        selectChange(val) {
            this.tableSelectList = val
        },
        //点击删除
        deletedVideo(row) {

        },
        //按钮改变
        switchChange($event, row) {
            this.$http({
                url: this.$http.adornUrl("/tigger/payvideo/updateShowStatus"),
                method: "post",
                params: {
                    id: row.id,
                    status: $event
                }
            }).then(({
                data
            }) => {
                this.getPayVideo()
            })
        },
        //获取所有的分类信息
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
        //修改提交
        updateForm(formName) {
            this.dialogTableVisible = false
            this.fullscreenLoading = true;
            let formDate = new FormData()
            //console.log(this.video)
            if (this.video.oid == null) {
                this.video.oid = 0
            }
            formDate.append('oid', this.video.oid)
            if (this.video.fileList != null) {
                formDate.append('files', this.video.fileList)
            }
            if (this.video.cover != null) {
                formDate.append('files', this.video.cover)
            }
            formDate.append('id', this.video.id)
            formDate.append('introduce', this.video.introduce)
            formDate.append('price', this.video.price)
            //this.video.cid = this.video.cid.split(",")
            formDate.append('cid', this.video.cid)
            formDate.append('homeShow', this.video.homeShow)
            formDate.append('point', this.video.point)
            formDate.append('sharePoint', this.video.sharePoint)
            formDate.append('byPoint', this.video.byPoint)
            formDate.append('byPointFather', this.video.byPointFather)
            //console.log(formDate)
            this.$http({
                url: this.$http.adornUrl("/tigger/payvideo/uploadVodie"),
                method: "post",
                data: formDate,
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded;'
                },

            }).then(({
                data
            }) => {
                this.fullscreenLoading = false;
                console.log(data)
                if (data.code == 0) {
                    this.video.fileList = null
                    this.$refs["video"].resetFields()
                    this.$refs["upload"].clearFiles()
                    this.$refs["upload1"].clearFiles()
                    Object.assign(this.video, this.$options.data().video)
                    this.$message({
                        message: '修改成功',
                        type: 'success'
                    });
                    this.getPayVideo()
                } else {

                    this.dialogTableVisible = true
                    this.$message({
                        message: '上传失败',
                        type: "warning"
                    });
                }
            })
        },
        dialogClose2() {
            // console.log(0)
            Object.assign(this.video, this.$options.data().video)
            this.getPayVideo()
            this.edit = false
        },

        //提交表单
        submitForm(formName) {
            this.$refs[formName].validate((valid) => {
                if (valid) {
                    this.uploadVideo()
                } else {
                    console.log('error submit!!');
                    return false;
                }
            });
        },
        //上传
        uploadVideo() {
            this.dialogTableVisible = false
            this.fullscreenLoading = true;

            let formDate = new FormData()
            formDate.append('files', this.video.fileList)
            formDate.append('oid', this.video.oid)
            formDate.append('files', this.video.cover)
            formDate.append('introduce', this.video.introduce)
            formDate.append('price', this.video.price)
            formDate.append('cid', this.video.cid)
            formDate.append('homeShow', this.video.homeShow)
            formDate.append('point', this.video.point)
            formDate.append('id', 0)
            formDate.append('sharePoint', this.video.sharePoint)
            formDate.append('byPoint', this.video.byPoint)
            formDate.append('byPointFather', this.video.byPointFather)
            this.$http({
                url: this.$http.adornUrl("/tigger/payvideo/uploadVodie"),
                method: "post",
                data: formDate,
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded;'
                },

            }).then(({
                data
            }) => {
                this.fullscreenLoading = false;
                if (data.code == 0) {

                    this.video.fileList = null
                    this.$refs["video"].resetFields()
                    this.$refs["upload"].clearFiles()
                    this.$refs["upload1"].clearFiles()

                    this.$message({
                        message: '上传成功',
                        type: 'success'
                    });
                    this.getPayVideo()
                } else {

                    this.dialogTableVisible = true
                    this.$message({
                        message: '上传失败',
                        type: "warning"
                    });
                }
            })

        },

        //文件上传钱前的方法
        onChangeImage(file, fileList) {
            //就绪状态
            if (file.status == "ready") {
                this.video.cover = file.raw
            }
            console.log(this.video.cover)

        },
        onChangeVideo(file, fileList) {
            if (file.status == "ready") {
                this.video.fileList = file.raw
            }
            //this.video.fileList = fileList
            console.log(this.video.fileList)
        },
        onRemoveImage(file, fileList) {
            this.video.cover = null
        },
        onRemoveVideo(file, fileList) {
            this.video.fileList = null
        },
        //点击页
        currentChange(val) {
            this.pageIndex = val;
            getPayVideo()
        },
        //点击页数
        sizeChange(val) {
            this.pageSize = val
            getPayVideo()
        },
        //获取视频
        getPayVideo() {
            this.$http({
                url: this.$http.adornUrl("/tigger/payvideo/list"),
                method: "get",
                params: this.$http.adornParams({
                    page: this.pageIndex,
                    limit: this.pageSize,
                })
            }).then(({
                data
            }) => {
                console.log(data)
                if (data.code == 0) {
                    this.totalPage = data.page.totalPage
                    this.dataList = data.page.list
                } else {
                    this.totalPage = 0
                    this.dataList = []
                }

            })
        },
        //点击播放
        playVideo(row) {
            this.row = row
            this.diaLogVideoFalag = true
        },
        //创建视频播放器
        createPlayer(playauth, raw) {
            var player = new Aliplayer({
                id: 'J_prismPlayer',
                width: '100%',
                autoplay: true,
                vid: raw.videoId,
                playauth: playauth,
                cover: raw.asrc,
                encryptType: 1, //当播放私有加密流时需要设置。
            }, function (player) {

            })
            this.player = player
        },
        //弹窗关闭
        dialogClose() {

            //销毁播放器
            this.player.dispose()
        },
        //弹窗开启
        dialogOpen() {
            this.$http({
                url: this.$http.adornUrl("/tigger/payvideo/getVideoVoucher"),
                method: 'get',
                params: this.$http.adornParams({
                    "videoId": this.row.videoId,
                })

            }).then(({
                data
            }) => {
                if (data.code == 0) {

                    this.createPlayer(data.msg, this.row)
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
        this.getPayVideo()
        this.selectOrgan()
    }, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>
