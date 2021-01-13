<template>
    <i-row>
        <i-col span="4">
            <i-menu active-name="1" width="auto">
                <i-menu-item name="1">光的散射</i-menu-item>
                <i-menu-item name="2">光的传播</i-menu-item>
                <i-menu-item name="3">光的色散</i-menu-item>
                <i-menu-item name="4">波粒二象性</i-menu-item>
            </i-menu>
            <!-- <Card id="left-part-card" :bordered="false" title="选择子讲座" icon="md-list" :padding="0" dis-hover>
                <CellGroup>
                    <Cell title="光的散射" label="已结束"/>
                    <Cell title="光的传播" label="或许可以写个状态什么的" />
                    <Cell title="光的色散" label="进行中" />
                    <Cell title="波粒二象性" label="进行中" />
                </CellGroup>
            </Card> -->
        </i-col>
        <i-col span="20" id="right-part-card">
            <i-card dis-hover :bordered="false">
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
                <Tabs value="name2">
                <TabPane label="报名管理" name="name2">
                    <Layout>
                        <Header class="tabHeader">
                            <Input class="searchBox" search enter-button="搜索" placeholder="请输入要搜索的信息" />
                            <Button class="outPutBtn" type="primary">导出报名信息</Button>
                        </Header>
                        <Content>
                            <Table stripe :columns="signUpCol" :data="signUpData"></Table>
                        </Content>
                    </Layout>
                </TabPane>
                <TabPane label="签到管理" name="name3">
                    <Layout>
                        <Header class="tabHeader">
                            <Input class="searchBox" search enter-button="搜索" placeholder="请输入要搜索的信息" />
                            <Button class="outPutBtn" type="primary">导出签到信息</Button>
                        </Header>
                        <Content>
                            <Table stripe :columns="signInCol" :data="signInData"></Table>
                        </Content>
                    </Layout>
                </TabPane>
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
            data1: [
                {
                    title: 'parent 1',
                    expand: true,
                    children: [
                        {
                            title: 'parent 1-1',
                            expand: true,
                            children: [
                                {
                                    title: 'leaf 1-1-1'
                                },
                                {
                                    title: 'leaf 1-1-2'
                                }
                            ]
                        },
                        {
                            title: 'parent 1-2',
                            expand: true,
                            children: [
                                {
                                    title: 'leaf 1-2-1'
                                },
                                {
                                    title: 'leaf 1-2-1'
                                }
                            ]
                        }
                    ]
                }
            ],
            signUpCol: [
                {
                    title: '姓名',
                    key: 'name'
                },
                {
                    title: '学号',
                    key: 'number'
                },
                {
                    title: '联系方式',
                    key: 'communicate'
                },
                {
                    title: '报名时间',
                    key: 'signUpTime'
                }
            ],
            signUpData: [
                {
                    name: '李子桐',
                    number: '22920192203999',
                    communicate: '15103128777',
                    signUpTime: '2021年1月12日 12:00:00'
                },
                {
                    name: '李子桐',
                    number: '22920192203999',
                    communicate: '15103128777',
                    signUpTime: '2021年1月12日 12:00:00'
                },
                {
                    name: '李子桐',
                    number: '22920192203999',
                    communicate: '15103128777',
                    signUpTime: '2021年1月12日 12:00:00'
                },
                {
                    name: '李子桐',
                    number: '22920192203999',
                    communicate: '15103128777',
                    signUpTime: '2021年1月12日 12:00:00'
                }
            ],
            signInCol: [
                {
                    title: '姓名',
                    key: 'name'
                },
                {
                    title: '学号',
                    key: 'number'
                },
                {
                    title: '联系方式',
                    key: 'communicate'
                },
                {
                    title: '签到时间',
                    key: 'signInTime'
                }
            ],
            signInData: [
                {
                    name: '李子桐',
                    number: '22920192203999',
                    communicate: '15103128777',
                    signInTime: '2021年1月12日 12:00:00'
                },
                {
                    name: '李子桐',
                    number: '22920192203999',
                    communicate: '15103128777',
                    signInTime: '2021年1月12日 12:00:00'
                },
                {
                    name: '李子桐',
                    number: '22920192203999',
                    communicate: '15103128777',
                    signInTime: '2021年1月12日 12:00:00'
                },
                {
                    name: '李子桐',
                    number: '22920192203999',
                    communicate: '15103128777',
                    signInTime: '2021年1月12日 12:00:00'
                }
            ],
            app
        }
    },
    updated () {
        // let rightHeight = document.getElementById("right-part-card").offsetHeight;
        // document.getElementById("left-part-card").style.height = rightHeight + 'px';
    }
}
</script>

<style>
.tabHeader {
    height: 50px;
    margin-left: 0;
    padding: 0;
    background-color: white;
}

.searchBox {
    float: left;
    width: 50%;
    height: 30px;
}

.outPutBtn {
    float: right;
}
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
