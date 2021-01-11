<template>
    <i-row>
        <i-card title="讲座列表">
            <i-row>
                <i-row type="flex" :gutter="16">
                    <i-col>
                        <i-button @click="showModal = true">新建讲座</i-button>
                    </i-col>
                    <i-col span="10">
                        <i-auto-complete icon="ios-search">
                            <div class="demo-auto-complete-item" :key="item.title" v-for="item in autoCompleteOptions">
                                <div class="demo-auto-complete-group">
                                    <span>{{ item.title }}</span>
                                    <!-- <a href="https://www.google.com/search?q=iView" target="_blank">更多</a> -->
                                </div>
                                <Option v-for="option in item.children" :value="option.title" :key="option.title">
                                    <span class="demo-auto-complete-title">{{ option.title }}</span>
                                    <span class="demo-auto-complete-count">{{ option.count }} 个结果</span>
                                </Option>
                            </div>
                        </i-auto-complete>
                    </i-col>
                    <i-col>
                        <i-button type="text" @click="advanceSearch = !advanceSearch">{{advanceSearch ? "高级搜索" : "普通搜索"}}</i-button>
                    </i-col>
                </i-row>
            </i-row>
            <i-row>
                <!-- filter -->
            </i-row>
            <i-divider />
            <i-form v-show="advanceSearch">
                <i-row type="flex" justify="space-between">
                    <i-col span="6">
                        <i-form-item label="期数" placeholder="请输入讲座期数">
                            <i-input />
                        </i-form-item>
                    </i-col>
                    <i-col span="6">
                        <i-form-item label="题目" placeholder="请输入讲座题目">
                            <i-input />
                        </i-form-item>
                    </i-col>
                    <i-col span="6">
                        <i-form-item label="汇报人" placeholder="请输入汇报人姓名">
                            <i-input />
                        </i-form-item>
                    </i-col>
                </i-row>
                <i-row type="flex" justify="space-between">
                    <i-col span="6">
                        <i-form-item label="时间" placeholder="请输入汇报时间">
                            <i-input />
                        </i-form-item>
                    </i-col>
                    <i-col span="6">
                        <i-form-item label="地点" placeholder="请输入讲座地点">
                            <i-input />
                        </i-form-item>
                    </i-col>
                    <i-col span="6"/>
                </i-row>
                <i-button type="primary">搜索</i-button>
                <i-button icon="ios-trash">清空条件</i-button>
            </i-form>
            <i-divider />
            <i-row>
                <i-table stripe :columns="LecturesCol" :data="LectureData">
                    <template slot-scope="{row}" slot="ope">
                        <a class="btn" href="javascript:;" @click="malert('详情')">[详情]</a>
                        <a class="btn" href="javascript:;" @click="malert('删除')">[删除]</a>
                    </template>
                </i-table>
            </i-row>
        </i-card>
        <i-modal v-model="showModal">
            <template slot="header">
                <span>新建讲座</span>
            </template>
            <i-form label-position="left" :label-width="100" :rules="formRule">
                <i-form-item label="讲座题目" prop="title">
                    <i-input />
                </i-form-item>
                <i-form-item label="期数" prop="count">
                    <i-input />
                </i-form-item>
                <i-form-item label="汇报人" prop="reporter">
                    <i-input />
                </i-form-item>
                <i-form-item label="时间" prop="time">
                    <i-input />
                </i-form-item>
                <i-form-item label="地点" prop="place">
                    <i-input />
                </i-form-item>
            </i-form>
            <template slot="footer">
                <Button type="primary">新建</Button>
                <Button type="default">取消</Button>
            </template>
        </i-modal>
    </i-row>
</template>

<script>
let _ = require("lodash");
export default {
    data () {
        return {
            advanceSearch: false,
            autoCompleteOptions: [
                {
                    title: "地点",
                    children: [
                        {
                            title: "科研1",
                            count: 10
                        }, {
                            title: "实验室2",
                            count: 10
                        }
                    ]
                }, {
                    title: "讲座名",
                    children: [
                        {
                            title: "科研纪要",
                            count: 10
                        }, {
                            title: "XXXX",
                            count: 10
                        }
                    ]
                }
            ],
            showModal: false,
            formRule: {
                title: [
                    {
                        required: true,
                        message: "必须输入讲座名",
                        trigger: "blur"
                    }
                ],
                count: [
                    {
                        required: true,
                        message: "必须输入讲座期数",
                        trigger: "blur"
                    }
                ],
                reporter: [
                    {
                        required: true,
                        message: "必须输入汇报人姓名",
                        trigger: "blur"
                    }
                ],
                time: [
                    {
                        required: true,
                        message: "必须输入时间",
                        trigger: "blur"
                    }
                ],
                place: [
                    {
                        required: true,
                        message: "必须输入地点",
                        trigger: "blur"
                    }
                ]
            },
            LecturesCol: [
                {
                    title: '题目',
                    key: 'title'
                },
                {
                    title: '讲座期数',
                    key: 'num'
                },
                {
                    title: '汇报人',
                    key: 'host'
                },
                {
                    title: '时间',
                    key: 'time'
                },
                {
                    title: '地点',
                    key: 'address'
                },
                {
                    title: '操作',
                    slot: 'ope'
                }
            ],
            LectureData: [
                {
                    title: '测试讲座01',
                    num: 10,
                    host: '李子桐',
                    time: '2021年1月11日 12:00:00 - 14:00:00',
                    address: '报告厅01'
                },
                {
                    title: '测试讲座02',
                    num: 10,
                    host: '李子桐',
                    time: '2021年1月11日 12:00:00 - 14:00:00',
                    address: '报告厅01'
                },
                {
                    title: '测试讲座03',
                    num: 10,
                    host: '李子桐',
                    time: '2021年1月11日 12:00:00 - 14:00:00',
                    address: '报告厅01'
                },
                {
                    title: '测试讲座04',
                    num: 10,
                    host: '李子桐',
                    time: '2021年1月11日 12:00:00 - 14:00:00',
                    address: '报告厅01'
                },
                {
                    title: '测试讲座05',
                    num: 10,
                    host: '李子桐',
                    time: '2021年1月11日 12:00:00 - 14:00:00',
                    address: '报告厅01'
                }
            ]
        }
    },
    methods: {
        setKeyword: _.debounce(function () {
            // do nothing
        }, 500),
        malert: function (i) {
            alert(i);
        }
    }
}
</script>

<style scoped>
    .demo-auto-complete-item{
        padding: 4px 0;
        border-bottom: 1px solid #F6F6F6;
    }
    .demo-auto-complete-group{
        font-size: 12px;
        padding: 4px 6px;
    }
    .demo-auto-complete-group span{
        color: #666;
        font-weight: bold;
    }
    .demo-auto-complete-group a{
        float: right;
    }
    .demo-auto-complete-count{
        float: right;
        color: #999;
    }
    .demo-auto-complete-more{
        display: block;
        margin: 0 auto;
        padding: 4px;
        text-align: center;
        font-size: 12px;
    }
</style>
