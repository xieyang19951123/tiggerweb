<template>
<div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
            <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
        </el-form-item>
        <el-form-item>
            <el-button @click="getDataList()">查询</el-button>
            <el-button v-if="isAuth('tigger:shopuser:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
            <el-button v-if="isAuth('tigger:shopuser:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
        </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column type="index" header-align="center" align="center" label="序号"></el-table-column>
        <el-table-column prop="psUsername" header-align="center" align="center" label="用户名"></el-table-column>
        <el-table-column prop="point" header-align="center" align="center" label="积分"></el-table-column>
        <el-table-column prop="recommend" header-align="center" align="center" label="推荐码"></el-table-column>
        <el-table-column prop="parentName" header-align="center" align="center" label="推荐人"></el-table-column>
        <el-table-column prop="balance" header-align="center" align="center" label="余额"></el-table-column>
        <el-table-column prop="" header-align="center" align="center" :formatter="stateFormat" label="是否是会员"></el-table-column>
        <el-table-column prop="createTime" header-align="center" align="center" label="创建时间"></el-table-column>
        <el-table-column prop="createTime" header-align="center" align="center" label="用户锁">
            <template slot-scope="scope">
                <el-switch v-model="scope.row.userRock" :inactive-value="0" :active-value="1" active-color="#13ce66" inactive-color="#ff4949" @change="switchChange($event,scope.row)">
                </el-switch>
            </template>
        </el-table-column>
        <el-table-column label="档案">
            <template slot-scope="scope">
                <el-button type="text" @click="recordclicke(scope.row.id)">档案</el-button>
            </template>
        </el-table-column>
        <el-table-column label="学习计划">
            <template slot-scope="scope">
                <el-button type="text" @click="stayplanclick(scope.row.id)">学习计划</el-button>
            </template>
        </el-table-column>
        <el-table-column label="家长信息">
            <template slot-scope="scope">
                <el-button type="text" @click="parentclick(scope.row.id)">家长信息</el-button>
            </template>
        </el-table-column>

        <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
            <template slot-scope="scope">
                <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
                <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
            </template>
        </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    <stayplan ref="stayplan" v-if="stayplancompent"></stayplan>
    <record ref="record" v-if="recordcompent"></record>
    <parentmessage ref="parentmessage" v-if="parentmessagecompent"></parentmessage>
</div>
</template>

<script>
import AddOrUpdate from "./shopuser-add-or-update";
import stayplan from "./stayplan"
import record from './record'
import parentmessage from './parentmessage'
export default {
    data() {
        return {
            dataForm: {
                key: "",
            },
            dataList: [],
            pageIndex: 1,
            pageSize: 10,
            totalPage: 0,
            dataListLoading: false,
            dataListSelections: [],
            addOrUpdateVisible: false,
            stayplancompent: false,
            recordcompent: false,
            parentmessagecompent: false
        };
    },
    components: {
        AddOrUpdate,
        stayplan,
        record,
        parentmessage
    },
    activated() {
        this.getDataList();
    },
    methods: {
        parentclick(id) {
            this.parentmessagecompent = true
            this.$nextTick(() => {
                this.$refs.parentmessage.getdatalist(id)
            })
        },
        recordclicke(id) {
            this.recordcompent = true
            this.$nextTick(() => {
                this.$refs.record.getlist(id)
            })
        },
        stayplanclick(id) {
            this.stayplancompent = true
            this.$nextTick(() => {
                this.$refs.stayplan.getStudentplan(id)
            })
        },
        switchChange(val, row) {
            this.$http({
                url: this.$http.adornUrl("/tigger/shopuser/switchChange"),
                method: "post",
                data: this.$http.adornData({
                    'id': row.id,
                    'rock': val
                })
            }).then(({
                data
            }) => {
                console.log(data)
                if (data.code == 0) {
                    if (val == 1) {
                        this.$alert(`初始化密码成功新密码为${data.password}`, '提示')
                    }
                    this.getDataList();
                }
            })
        },
        // 获取数据列表
        getDataList() {
            this.dataListLoading = true;
            this.$http({
                url: this.$http.adornUrl("/tigger/shopuser/list"),
                method: "get",
                params: this.$http.adornParams({
                    page: this.pageIndex,
                    limit: this.pageSize,
                    key: this.dataForm.key,
                }),
            }).then(({
                data
            }) => {
                console.log(data)
                if (data && data.code === 0) {
                    this.dataList = data.page.list;
                    this.totalPage = data.page.totalCount;
                } else {
                    this.dataList = [];
                    this.totalPage = 0;
                }
                this.dataListLoading = false;
            });
        },
        //是否会员
        stateFormat(row, column) {
            if (row.shopVip == 1) {
                return "会员";
            } else {
                return "非会员";
            }
        },
        // 每页数
        sizeChangeHandle(val) {
            this.pageSize = val;
            this.pageIndex = 1;
            this.getDataList();
        },
        // 当前页
        currentChangeHandle(val) {
            this.pageIndex = val;
            this.getDataList();
        },
        // 多选
        selectionChangeHandle(val) {
            this.dataListSelections = val;
        },
        // 新增 / 修改
        addOrUpdateHandle(id) {
            this.addOrUpdateVisible = true;
            this.$nextTick(() => {
                this.$refs.addOrUpdate.init(id);
            });
        },
        // 删除
        deleteHandle(id) {
            var ids = id ? [id] :
                this.dataListSelections.map((item) => {
                    return item.id;
                });
            this.$confirm(
                `确定对[id=${ids.join(",")}]进行[${id ? "删除" : "批量删除"}]操作?`,
                "提示", {
                    confirmButtonText: "确定",
                    cancelButtonText: "取消",
                    type: "warning",
                }
            ).then(() => {
                this.$http({
                    url: this.$http.adornUrl("/tigger/shopuser/delete"),
                    method: "post",
                    data: this.$http.adornData(ids, false),
                }).then(({
                    data
                }) => {
                    if (data && data.code === 0) {
                        this.$message({
                            message: "操作成功",
                            type: "success",
                            duration: 1500,
                            onClose: () => {
                                this.getDataList();
                            },
                        });
                    } else {
                        this.$message.error(data.msg);
                    }
                });
            });
        },
    },
};
</script>
