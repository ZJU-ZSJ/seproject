<template>
    <div>
        <Stocksell
            :visible="isshow"
            :stockid="nowid"
            :cardid="nowcard"
            v-if="isshow"
            @cancel="closeModal"/>
        <a-list
                itemLayout="horizontal"
                :dataSource="data"
                :pagination="pagination"
        >
            <a-list-item slot="renderItem" slot-scope="item, index">
                <a-card
                        :title="'股票名: ' + item.stock_name + ', 股票代码: ' + item.stock_id"
                        :bordered=true
                        style="width: 100%;">
                    <a-card-grid style="width:50%;textAlign:'center'">
                        <p style="font-weight: bolder">数量:{{item.num}}</p>
                    </a-card-grid>
                    <a-card-grid style="width:50%;textAlign:'center'">
                        <p style="font-weight: bolder">冻结数量:{{item.freeze_num}}</p>
                    </a-card-grid>
                    <a-card-grid style="width:50%;textAlign:'center'">
                        <p style="font-weight: bolder">发行量:{{item.stock_sum}}</p>
                    </a-card-grid>
                    <a-card-grid style="width:50%;textAlign:'center'">
                        <p style="font-weight: bolder">类型:{{item.stock_type}}</p>
                    </a-card-grid>
                    <a-card-grid style="width:100%;textAlign:'center'">
                        <a-divider type="vertical"/>
                        <router-link :to="'/stock/'+ item.stock_id">
                            <a-button type="primary">查看详情</a-button>
                        </router-link>
                        <a-divider type="vertical"/>
                        <a-button type="primary" @click="showModal(item.stock_id,item.card_id)">出售股票</a-button>
                    </a-card-grid>
                </a-card>
            </a-list-item>
        </a-list>
    </div>
</template>

<script>
    import Stocksell from "@/components/Stocksell"
    export default {
        name: "Stockinfo",
        components:{Stocksell},
        data() {
            return {
                nowid:null,
                nowcard:null,
                isshow:false,
                data: [],
                pagination: {
                    pageSize: 5, // 默认每页显示数量
                    showSizeChanger: true, // 显示可改变每页数量
                    pageSizeOptions: ['5', '10', '20', '30'], // 每页数量选项
                    showTotal: total => `Total ${total} items`, // 显示总数
                    showSizeChange: (current, pageSize) => this.pageSize = pageSize, // 改变每页数量时更新显示
                }
            }
        },
        mounted() {
            this.init();
        },
        methods: {
            showModal(id,card_id) {
                console.log(id);
                this.nowid = id;
                this.nowcard = card_id;
                this.isshow = true
            },
            closeModal() {
                this.nowid = null;
                this.nowcard = null;
                this.isshow = false
            },
            init() {

                this.$axios
                    .get(this.baseurl + "/api/get_all_capital",{
                        params:{
                            "user_id":this.$cookies.get("user_id")
                        }
                    })
                    .then(
                        response => {
                            if (response.data.code === 0) {
                                this.fund = response.data.data;
                                let data = new FormData();
                                data.append("type", "stock");
                                data.append("account_id", JSON.stringify(this.fund));
                                this.$axios
                                    .post(this.baseurl + "/api/get_account_stock",data)
                                    .then(
                                        response => {
                                                this.data = response.data;
                                        }
                                    );
                            } else {
                                this.$message.error(response.data.msg);
                            }
                        }
                    );

                /*
                this.$axios
                    .get(this.baseurl + "/api/search_security",{
                        params:{
                            "user_id":this.$cookies.get("user_id")
                        }
                    })
                    .then(
                        response => {
                            if (response.data.code === 0) {
                                this.data = response.data.data.stock;
                            } else {
                                this.$message.error(response.data.msg);
                            }
                        }
                    );
                */

            },
        }
    }
</script>

<style scoped>
</style>
