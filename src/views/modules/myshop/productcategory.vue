<!--  -->
<template>
<div>
    <el-button type="success" @click="addParent">添加父级菜单</el-button>
    <el-button type="danger" @click="()=> remove()">批量删除</el-button>
    <el-tree ref="tree" :data="data" show-checkbox node-key="id" :expand-on-click-node="false" @node-contextmenu="nodeContextmenu" :props="defaultProps" :default-expanded-keys="expandedKeys">
        <div slot-scope="{ node, data }" style="width:100%">
            <span class="custom-tree-node">
                <span>{{ node.label }}</span>
                <svg class="icon" aria-hidden="true" @click="cliclSvg($event)" v-show="node.level >1">
                    <use :href="data.icon"></use>
                </svg>
                <span>
                    <el-button type="text" size="mini" @click="() => append(node,data)">添加</el-button>
                    <el-button type="text" size="mini" @click="() => remove( data)">删除</el-button>
                    <el-button type="text" size="mini" @click="() => update( data)">修改</el-button>
                </span>
            </span>
        </div>
    </el-tree>

    <el-dialog title="添加分类" :visible.sync="dialogVisible" width="17%">
        <el-form>
            <el-form-item>
                <el-input v-model="category.name" placeholder="请输入内容"></el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="innerVisible=true"> 选择图标</el-button>
            </el-form-item>
            <el-form-item>
                <svg class="icon1" aria-hidden="true">
                    <use :href="category.icon"></use>
                </svg>
            </el-form-item>
        </el-form>
        <el-dialog title="选择图标" :visible.sync="innerVisible" append-to-body>
            <div>
                <el-button class="butn "> <svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongshouhui-fangzi"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongnaiguo"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon--fen-"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongjiaoyu"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongyimiao"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongxie"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongfang"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongbaojianke"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongyaoma"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongcanju"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-ertongmianxie"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-yuwen"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-gangqin-yuanwenjian"></use>
                    </svg></el-button>
                <el-button class="butn "><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-shuxue"></use>
                    </svg></el-button>
                <el-button class="butn"><svg class="icon1" aria-hidden="true" @click="cliclSvg($event)">
                        <use xlink:href="#icon-shangxue"></use>
                    </svg></el-button>

            </div>

        </el-dialog>
        <span slot="footer" class="dialog-footer" v-if="edit">
            <el-button @click="innerVisible = false">取 消</el-button>
            <el-button type="primary" @click="editCategory">修改</el-button>
        </span>
        <span slot="footer" class="dialog-footer" v-if="!edit">
            <el-button @click="innerVisible = false">取 消</el-button>
            <el-button type="primary" @click="insertCategory">确 定</el-button>
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

    //这里存放数据

    data() {
        return {
            edit: false,
            data: [],
            defaultProps: {
                children: "chidrens",
                label: "name",

            },
            expandedKeys: [], //展示的节点
            checkChangeDatas: [], //选中的节点
            fit: "fit",
            dialogVisible: false,

            innerVisible: false,

            category: {
                icon: "#icon-ertongshouhui-fangzi",
                name: "",
                parentId: null,
                catLevel: null,
                showStatus: 1,
                id: null,
                astatus: 2
            }
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        insertCategory() {
            console.log(this.category)
            if (this.category.name == "") {
                this.$message({
                    message: "请输入名称",
                    type: "warning"
                })
                return;
            }
            this.dialogVisible = false;
            this.$http({
                url: this.$http.adornUrl("/tigger/category/save"),
                method: "post",
                data: this.$http.adornData(this.category),
            }).then(({
                data
            }) => {
                console.log(data);
                if (data.code == 0) {
                    this.category.name = ""
                    this.getDataList();
                    this.expandedKeys = [this.category.id];
                    this.$message({
                        message: "添加成功",
                        type: "success"
                    })
                }
            });
        },
        cliclSvg(event) {
            // console.log(123)
            let tagUse = event.target.getElementsByTagName("use")
            console.log(tagUse)
            if (typeof (event.target.href) == 'undefined') {
                if (tagUse.length > 0) {

                    this.category.icon = tagUse[0].href.baseVal
                }

            } else {

                this.category.icon = event.target.href.baseVal
            }
            this.innerVisible = false
        },
        nodeContextmenu(event, data, node, mu) {
            console.log(data.id);
        },
        //获取所有的数据
        getDataList() {
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
                if (data.code == 0) {}
                this.data = data.page;
                Object.assign(this.category, this.$options.data().category)
            });
        },
        //添加新的节点
        append(node, data) {
            if (node.level > 1) {
                this.$message({
                    message: "最高级数为2级",
                    type: "warning",
                });
                return;
            }
            if (node.level == 1) {
                this.dialogVisible = true
                this.category.parentId = data.id
                this.category.catLevel = node.level + 1
                this.category.id = node.data.id
                return
            }
            this.$prompt(`请输入子级`, `父级${data.name}`, {
                confirmButtonText: "确定",
                cancelButtonText: "取消",
                inputPattern: /\S/,
                inputErrorMessage: "输入不能为空",
            }).then(({
                value
            }) => {
                this.$http({
                    url: this.$http.adornUrl("/tigger/category/save"),
                    method: "post",
                    data: this.$http.adornData({
                        name: value,
                        parentId: data.id,
                        catLevel: node.level + 1,
                        showStatus: 1,
                        astatus: 2
                    }),
                }).then(({
                    data
                }) => {
                    console.log(data);
                    if (data.code == 0) {
                        this.getDataList();
                        this.expandedKeys = [node.data.id];
                    }
                });
            });
        },
        //删除节点
        remove(data) {
            var ids = data ? [data.id] : this.$refs.tree.getCheckedKeys(true);
            this.$confirm("是否删除", "提示", {
                confirmButtonText: "确定",
                cancelButtonText: "取消",
                type: "warning",
            }).then(() => {
                this.$http({
                    url: this.$http.adornUrl("/tigger/category/delete"),
                    method: "post",
                    data: this.$http.adornData(ids, false),
                }).then(({
                    data
                }) => {
                    if (data.code == 0) {
                        this.$message({
                            message: "删除成功",
                            type: "success",
                        });
                        this.getDataList();
                    }
                });
            });
        },
        //添加父级节点
        addParent() {
            this.$prompt(`请输入父级`, `提示`, {
                confirmButtonText: "确定",
                cancelButtonText: "取消",
                inputPattern: /\S/,
                inputErrorMessage: "输入不能为空",
            }).then(({
                value
            }) => {
                this.$http({
                    url: this.$http.adornUrl("/tigger/category/save"),
                    method: "post",
                    data: this.$http.adornData({
                        name: value,
                        parentId: 0,
                        catLevel: 1,
                        showStatus: 1,
                        astatus: 2
                    }),
                }).then(({
                    data
                }) => {
                    console.log(data);
                    if (data.code == 0) {
                        this.getDataList();
                        this.expandedKeys = [node.data.id];
                    }
                });
            });
        },
        update(data) {
            console.log(data)
            if (data.catLevel == 1) {
                this.$prompt(`请输入父级`, `提示`, {
                    confirmButtonText: "确定",
                    cancelButtonText: "取消",
                    inputPattern: /\S/,
                    inputErrorMessage: "输入不能为空",
                }).then(({
                    value
                }) => {
                    data.name = value
                    this.$http({
                        url: this.$http.adornUrl("/tigger/category/update"),
                        method: "post",
                        data: this.$http.adornData(data)
                    }).then(({
                        data
                    }) => {
                        this.getDataList();
                    })
                })
            } else {
                this.category = data
                this.dialogVisible = true
                this.edit = true
            }

        },
        editCategory() {
            //data.name = value
            this.$http({
                url: this.$http.adornUrl("/tigger/category/update"),
                method: "post",
                data: this.$http.adornData(this.category)
            }).then(({
                data
            }) => {
                this.dialogVisible = false
                this.getDataList();
            })
        }
    },
    //生命周期 - 创建完成（可以访问当前this实例）
    created() {},
    //生命周期 - 挂载完成（可以访问DOM元素）
    mounted() {},
    beforeCreate() {}, //生命周期 - 创建之前
    beforeMount() {}, //生命周期 - 挂载之前
    beforeUpdate() {}, //生命周期 - 更新之前
    updated() {}, //生命周期 - 更新之后
    beforeDestroy() {}, //生命周期 - 销毁之前
    destroyed() {}, //生命周期 - 销毁完成
    activated() {
        this.getDataList();
    }, //如果页面有keep-alive缓存功能，这个函数会触发
};
</script>

<style>
.custom-tree-node {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 14px;
    padding-right: 14px;

}

.icon {
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
}

.icon1 {
    width: 5em;
    height: 5em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;

}

.butn {
    padding: 0.3125rem;
    margin: 10px 10px 10px 10px;

}

.iconfont {
    font-family: "iconfont" !important;
    font-size: 1em;
    font-style: normal;
    -webkit-font-smoothing: antialiased;
    -webkit-text-stroke-width: 0.2px;
    -moz-osx-font-smoothing: grayscale;
}
</style>
