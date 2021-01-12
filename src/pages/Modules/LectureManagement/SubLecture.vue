<template>
    <i-row>
        <i-col span="5" style="height: 100%">
            <Card id="left-part-card" :bordered="false" title="子讲座列表" icon="md-list" :padding="0" dis-hover>
            <CellGroup>
                <Cell title="光的散射" label="已结束"/>
                <Cell title="光的传播" label="或许可以写个状态什么的" />
                <Cell title="光的色散" label="进行中" />
                <Cell title="波粒二象性" label="进行中" />
            </CellGroup>
        </Card>
        </i-col>
        <i-col span="19">
            <i-card id="right-part-card" dis-hover :bordered="false">
                <i-row type="flex">
                    <i-col span="1">
                        <i-avatar :src="app.webInfo.avatar" />
                    </i-col>
                    <i-col span="23">
                        <i-row style= "margin-bottom: 16px;" type="flex" justify="space-between" align="middle">
                            <i-col>
                                <span class="head-title">讲座管理：第一节课（光的散射）</span>
                            </i-col>
                            <i-col>
                                <i-button type="primary">确认修改</i-button>
                            </i-col>
                        </i-row>
                        <i-row type="flex" justify="space-between">
                            <i-col span="18" id="sub-lecture-detail">
                                <i-form label-position="left" :label-width="120" :label-colon="true">
                                    <i-row type="flex" justify="space-between">
                                        <i-col span="10">
                                            <i-form-item label="汇报题目">
                                                <i-input size="small" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="讲座期数">
                                                <i-input size="small" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="汇报人姓名">
                                                <i-input size="small" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="汇报时间">
                                                <i-input size="small" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="汇报地点">
                                                <i-input size="small" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="预约开始时间">
                                                <i-input size="small" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="预约结束时间">
                                                <i-input size="small" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="可预约人数">
                                                <i-input size="small" />
                                            </i-form-item>
                                        </i-col>
                                    </i-row>
                                </i-form>
                            </i-col>
                            <i-col>
                                <p style="color: rgb(128, 134, 149);">状态</p>
                                <p style="font-size: 24px;">OK</p>
                            </i-col>
                        </i-row>
                    </i-col>
                </i-row>
                <Tabs value="name1">
                    <TabPane label="报名管理" name="name2">标签二的内容</TabPane>
                    <TabPane label="签到管理" name="name3">标签三的内容</TabPane>
                </Tabs>
            </i-card>
        </i-col>
    </i-row>
</template>

<script>
const app = require("@/config")
export default {
    data () {
        return {
            app
        }
    },
    mounted () {
        let rightHeight = document.getElementById("right-part-card").offsetHeight;
        document.getElementById("left-part-card").style.height = rightHeight + 'px';
    }
}
</script>

<style>
    .head-title {
        display: inline-block;
        color: #17233d;
        font-weight: 500;
        font-size: 20px;
    }
    #sub-lecture-detail .ivu-form-item-label {
        color: #17233d;
    }
</style>
