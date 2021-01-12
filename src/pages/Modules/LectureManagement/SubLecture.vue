<template>
    <i-row>
        <i-col span="5">
            <Tree :data="data1" expand-node></Tree>
        </i-col>
        <i-col span="19">
            <i-card title="讲座详情">
            <Tabs value="name1">
                <TabPane label="详细信息" name="name1">标签一的内容</TabPane>
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
            ]
        }
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
</style>
