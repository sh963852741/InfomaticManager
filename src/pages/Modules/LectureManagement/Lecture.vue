<template>
    <i-row>
        <i-card title="讲座列表">
            <i-row>
                <i-row type="flex" :gutter="16">
                    <i-col>
                        <i-button @click="showModal = true" type="primary">新建讲座</i-button>
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
                        <i-button type="text" @click="advanceSearch = !advanceSearch">{{advanceSearch ? "普通搜索" : "高级搜索"}}</i-button>
                    </i-col>
                </i-row>
            </i-row>
            <i-row>
                <!-- filter -->
            </i-row>
            <i-divider />
            <i-form v-show="advanceSearch" label-position="top">
                <i-row type="flex" justify="space-between">
                    <i-col span="6">
                        <i-form-item label="期数" placeholder="请输入讲座期数">
                            <i-input v-model="searchCondition.serial" />
                        </i-form-item>
                    </i-col>
                    <i-col span="6">
                        <i-form-item label="题目" placeholder="请输入讲座题目">
                            <i-input v-model="searchCondition.name" />
                        </i-form-item>
                    </i-col>
                    <i-col span="6">
                        <i-form-item label="汇报人" placeholder="请输入汇报人姓名">
                            <i-input v-model="searchCondition.hoster" />
                        </i-form-item>
                    </i-col>
                </i-row>
                <i-row type="flex" justify="space-between">
                    <i-col span="6">
                        <i-form-item label="时间" placeholder="请输入汇报时间">
                            <i-date-picker style="width: 100%;" v-model="searchCondition.date" type="daterange" separator=" 至 " />
                        </i-form-item>
                    </i-col>
                    <i-col span="6">
                        <i-form-item label="地点" placeholder="请输入讲座地点">
                            <i-input />
                        </i-form-item>
                    </i-col>
                    <i-col span="6"/>
                </i-row>
                <i-button type="primary" @click="getLecture()">搜索</i-button>
                <i-button icon="ios-trash" @click="clearSearch()">清空条件</i-button>
            </i-form>
            <i-divider />
            <i-row type="flex" justify="end" :gutter="16" style="margin-bottom: 8px;">
                <i-col><i-button type="primary" icon="ios-cloud-upload-outline">导入列表</i-button></i-col>
                <i-col><i-button type="primary" icon="ios-cloud-download-outline">导出列表</i-button></i-col>
            </i-row>
            <i-row>
                <i-table :loading="tableLoading" stripe :columns="LecturesCol" :data="LectureData">
                    <template slot-scope="{row}" slot="ope">
                        <a class="btn" href="javascript:;" @click="toDetail(row.ID)">[子讲座]</a>
                        <a class="btn" href="javascript:;" @click="modifyLecture(row)">[修改]</a>
                        <a class="btn" href="javascript:;" @click="deleteLecture(row.ID)">[删除]</a>
                    </template>
                </i-table>
            </i-row>
        </i-card>
        <i-modal v-model="showModal">
            <template slot="header">
                <span>新建/修改讲座</span>
            </template>
            <i-form ref="LectureForm" label-position="left" :label-width="100" :rules="formRule" :model="lecture">
                <i-form-item label="讲座题目" prop="title">
                    <i-input v-model="lecture.title" />
                </i-form-item>
                <i-form-item label="期数" prop="count">
                    <i-input v-model="lecture.count" />
                </i-form-item>
                <i-form-item label="汇报人" prop="reporter">
                    <i-input v-model="lecture.reporter" />
                </i-form-item>
                <i-form-item label="开始时间">
                    <i-date-picker v-model="lecture.beginTime" style="width: 100%"/>
                </i-form-item>
                <i-form-item label="结束时间">
                    <i-date-picker v-model="lecture.endTime" style="width: 100%"/>
                </i-form-item>
                <i-form-item label="地点" prop="place">
                    <i-input v-model="lecture.place" />
                </i-form-item>
            </i-form>
            <template slot="footer">
                <Button type="primary" :loading="savingLecture" @click="saveLecture">保存</Button>
                <Button type="default" @click="cancelLecture">取消</Button>
            </template>
        </i-modal>
    </i-row>
</template>

<script>
let _ = require("lodash");
const axios = require("axios");
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
            lecture: {
                id: "",
                title: "",
                count: "",
                place: "",
                reporter: "",
                beginTime: "",
                endTime: ""
            },
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
                    key: 'Name'
                },
                {
                    title: '讲座期数',
                    key: 'Serial'
                },
                {
                    title: '汇报人',
                    key: 'Hoster'
                },
                {
                    title: '开始时间',
                    key: 'BeginOn'
                },
                {
                    title: '结束时间',
                    key: 'EndOn'
                },
                {
                    title: '地点',
                    key: 'Address'
                },
                {
                    title: '操作',
                    slot: 'ope'
                }
            ],
            LectureData: [],
            savingLecture: false,
            tableLoading: false,
            searchCondition: {}
        }
    },
    created () {
        this.getLecture();
    },
    methods: {
        deleteLecture (ID) {
            this.$Modal.confirm({
                title: "确认删除",
                content: "确实要删除本讲座吗？此操作不可恢复。",
                onOk: () => {
                    axios.post("/api/activity/RemoveActivityCategory", {id: ID}, msg => {
                        if (msg.success) {
                            this.$Message.success("删除成功");
                            this.getLecture();
                        } else {
                            this.$Message.error(msg.msg);
                        }
                    })
                }
            })
        },
        toDetail (ID) {
            this.$router.push({name: "SubActivityManager", query: {id: ID}});
        },
        getLecture () {
            this.tableLoading = true;
            axios.post("/api/activity/GetAcitvities", {...this.searchCondition}, msg => {
                this.tableLoading = false;
                if (msg.success) {
                    this.LectureData = msg.data;
                } else {
                    this.$Message.error(msg.msg);
                }
            })
        },
        saveLecture () {
            let form = this.$refs["LectureForm"];
            form.validate((valid) => {
                if (valid) {
                    this.savingLecture = true;
                    axios.post("/api/activity/SaveActivityCategory", {
                        ID: this.lecture.id,
                        Name: this.lecture.title,
                        BeginOn: this.lecture.beginTime,
                        EndOn: this.lecture.endTime,
                        Address: this.lecture.place,
                        Serial: this.lecture.count,
                        Hoster: this.lecture.reporter
                    }, msg => {
                        this.savingLecture = false;
                        if (msg.success) {
                            this.$Message.success("保存成功");
                            this.showModal = false;
                            this.getLecture();
                        } else {
                            this.$Message.error(`${msg.msg}：${msg.errors}`);
                        }
                    })
                }
            })
        },
        cancelLecture () {
            let form = this.$refs["LectureForm"];
            form.resetFields();
            this.showModal = false;
        },
        setKeyword: _.debounce(function () {
            // do nothing
        }, 500),
        modifyLecture (src) {
            this.lecture.id = src.ID;
            this.lecture.title = src.Name;
            this.lecture.beginTime = src.BeginOn;
            this.lecture.endTime = src.EndOn;
            this.lecture.place = src.Address;
            this.lecture.count = src.Serial;
            this.lecture.reporter = src.Hoster;
            this.showModal = true;
        },
        clearSearch () {
            this.searchCondition = {};
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
