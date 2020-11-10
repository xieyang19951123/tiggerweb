<template>
<div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
            <el-input v-model="dataForm.key" placeholder="用户名" clearable></el-input>
        </el-form-item>

        <el-form-item>
            <el-select v-model="dataForm.payType" placeholder="请选择支付类型" clearable>
                <el-option v-for="item in typeList" :key="item.value" :label="item.label" :value="item.value"></el-option>

            </el-select>
        </el-form-item>

        <el-form-item>
            <el-select v-model="dataForm.payStatus" placeholder="请选择支付状态" clearable>
                <el-option v-for="item in statusList" :key="item.value" :label="item.label" :value="item.value"></el-option>

            </el-select>
        </el-form-item>
        <el-form-item>
            <el-button @click="getDataList()">查询</el-button>
        </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column type="index" header-align="center" align="center" label="序号"></el-table-column>
        <el-table-column prop="ocode" header-align="center" align="center" label="订单号"></el-table-column>
        <el-table-column prop="userName" header-align="center" align="center" label="用户名"></el-table-column>
        <el-table-column prop="productName" header-align="center" align="center" label="商品名"></el-table-column>
        <el-table-column prop="omoney" header-align="center" align="center" label="金额"></el-table-column>
        <el-table-column prop="numbers" header-align="center" align="center" label="商品数量"></el-table-column>
        <el-table-column prop="address" header-align="center" align="center" label="收货地址" :formatter="getorder"></el-table-column>
        <el-table-column prop="paystatus" header-align="center" align="center" label="支付状态" :formatter="getPyStatus"></el-table-column>
        <el-table-column prop="payType" header-align="center" align="center" label="支付类型" :formatter="getPayType"></el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
</div>
</template>

<script>
//import AddOrUpdate from "./order-add-or-update";
export default {
    data() {
        return {
            dataForm: {
                key: "",
                payType: "",
                payStatus: "",
            },
            dataList: [],
            pageIndex: 1,
            pageSize: 10,
            totalPage: 0,
            dataListLoading: false,
            dataListSelections: [],
            addOrUpdateVisible: false,
            typeList: [{
                    value: 1,
                    label: "会费",
                },
                {
                    value: 2,
                    label: "商品支付",
                },
            ],
            statusList: [{
                    value: 1,
                    label: "未支付",
                },
                {
                    value: 2,
                    label: "已支付",
                },
                {
                    value: 3,
                    label: "商家已发货",
                },
                {
                    value: 4,
                    label: "订单完成",
                },
            ],
        };
    },
    components: {},
    activated() {
        this.getDataList();
    },
    methods: {
        // 获取数据列表
        getDataList() {
            this.dataListLoading = true;

            this.$http({
                url: this.$http.adornUrl("/tigger/order/list"),
                method: "get",
                params: this.$http.adornParams({
                    page: this.pageIndex,
                    limit: this.pageSize,
                    key: this.dataForm.key,
                    payType: this.dataForm.payType,
                    payStatus: this.dataForm.payStatus,
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
                console.log(this.dataList);
                this.dataListLoading = false;
            });
        },
        //支付状态
        getPyStatus(row, column) {
            if (row.payStatus == 1) {
                return "未支付";
            } else if (row.payStatus == 2) {
                return "已支付";
            } else if (row.payStatus == 3) {
                return "商家已发货";
            } else if (row.payStatus == 4) {
                return "订单完成";
            }
        },
        //支付状态
        getPayType(row, column) {
            if (row.payType == 1) {
                return "会费";
            } else if (row.payType == 2) {
                return "商品支付";
            }
        },
        getorder(row, column) {
            let username = row.adusername == null ? "" : row.adusername
            let iphone = row.iphone == null ? "" : row.iphone
            let address = row.address == null ? "" : row.address
            return username + " " + iphone + " " + address
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
                    url: this.$http.adornUrl("/shopvip/order/delete"),
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
