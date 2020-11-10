<!--  -->
<template>
<div class=''>
    <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
        <quill-editor ref="text" v-model="messageEntity.message" clacss="myQuillEditor" :options="editorOption" />
        <el-button type="primary" @click="save()" style="margin:right">确定</el-button>
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
        //这里存放数据
        return {
            dialogVisible: false,
            content: '',
            editorOption: {},
            messageEntity: {
                message: '',
                id: 0,
                pid: 0
            }
        };
    },
    //监听属性 类似于data概念
    computed: {},
    //监控data中的数据变化
    watch: {},
    //方法集合
    methods: {
        getdata(id) {
            console.log(id)
            this.messageEntity.pid = id
            this.dialogVisible = true
            this.$http({
                url: this.$http.adornUrl('/tigger/message/get'),
                method: 'get',
                params: this.$http.adornParams({
                    pid: id
                })
            }).then(({
                data
            }) => {
                if (data.code == 0) {
                    if (data.page != null || data.page != undefined) {
                        this.messageEntity.message = data.page.message
                        this.messageEntity.id = data.page.id

                    }
                }
            })
        },
        save() {
            this.$http({
                url: this.$http.adornUrl(`/tigger/message/${this.messageEntity.id==0?'save':"edit"}`),
                method: 'post',
                //params: this.http.adornParams({})
                data: this.$http.adornData({
                    message: this.messageEntity.message,
                    id: this.messageEntity.id,
                    pid: this.messageEntity.pid
                })
            }).then(({
                data
            }) => {
                if (data.code == 0 && data.page.id != 0) {
                    this.messageEntity.id = data.page.id
                    this.dialogVisible = false
                }
            });
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
