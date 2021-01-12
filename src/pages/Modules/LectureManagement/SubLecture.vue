<template>
    <i-row>
        <i-col span="5">
            <Card title="子讲座列表" icon="ios-options" :padding="0" dis-hover>
            <CellGroup>
                <Cell title="Only show titles" />
                <Cell title="Display label content" label="label content" />
                <Cell title="Display right content" extra="details" />
                <Cell title="Link" extra="details" to="/components/button" />
                <Cell title="Open link in new window" to="/components/button" target="_blank" />
                <Cell title="Disabled" disabled />
                <Cell title="Selected" selected />
                <Cell title="With Badge" to="/components/badge">
                    <Badge :count="10" slot="extra" />
                </Cell>
                <Cell title="With Switch">
                    <Switch v-model="switchValue" slot="extra" />
                </Cell>
            </CellGroup>
        </Card>
        </i-col>
        <i-col span="19">
            <i-card dis-hover>
                <i-row type="flex">
                    <i-col span="1">
                        <i-avatar />
                    </i-col>
                    <i-col span="23">
                        <i-row style= "margin-bottom: 16px;" type="flex" justify="space-between" align="middle">
                            <i-col>
                                讲座管理：第一节课（光的散射）
                            </i-col>
                            <i-col>
                                <i-button type="primary">确认修改</i-button>
                            </i-col>
                        </i-row>
                        <i-row type="flex" justify="space-between">
                            <i-col span="18">
                                <i-form label-position="left" :label-width="120" :label-colon="true">
                                    <i-row type="flex" justify="space-between">
                                        <i-col span="10">
                                            <i-form-item label="回报题目">
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
                                <span>状态：</span>
                                <br />
                                <span>OK</span>
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
export default {
    data () {
        return {
        }
    }
}
</script>

<style>

</style>
