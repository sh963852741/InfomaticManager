<template>
    <div>
        <i-card title="讲座列表">
            <div>
                <!-- <i-auto-complete icon="ios-search" @on-change="calcSearchOptions" @on-select="search">
                    <div class="demo-auto-complete-item" :key="item.title" v-for="item in autoCompleteOptions">
                        <div class="demo-auto-complete-group">
                            <span>{{ item.title }}</span>
                            <a href="https://www.google.com/search?q=iView" target="_blank">更多</a>
                        </div>
                        <Option v-for="option in item.children" :label="option.title" :value="option" :key="option.title">
                            <span class="demo-auto-complete-title">{{ option.title }}</span>
                            <span class="demo-auto-complete-count">{{ option.count }} 个结果</span>
                        </Option>
                    </div>
                </i-auto-complete> -->
                <i-row v-if="!advanceSearch" type="flex" :gutter="16">
                    <i-col span="8">
                        <i-input search @on-search="getLecture()" v-model="searchCondition.name" placeholder="搜索讲座题目"/>
                    </i-col>
                    <i-col>
                        <i-button type="text" @click="advanceSearch = !advanceSearch">{{advanceSearch ? "普通搜索" : "高级搜索"}}</i-button>
                    </i-col>
                </i-row>
                <i-form v-else label-position="top">
                    <i-row type="flex" justify="space-between">
                        <i-col span="6">
                            <i-form-item label="题目">
                                <i-input v-model="searchCondition.name" placeholder="请输入讲座题目"/>
                            </i-form-item>
                        </i-col>
                        <i-col span="6">
                            <i-form-item label="期数">
                                <i-input v-model="searchCondition.serial" placeholder="请输入讲座期数" />
                            </i-form-item>
                        </i-col>
                        <i-col span="6">
                            <i-form-item label="汇报人">
                                <i-input v-model="searchCondition.hoster" placeholder="请输入汇报人姓名"/>
                            </i-form-item>
                        </i-col>
                    </i-row>
                    <i-row type="flex" justify="space-between">
                        <i-col span="6">
                            <i-form-item label="开始时间">
                                <i-date-picker style="width: 100%;" v-model="searchCondition.date" type="daterange" separator=" 至 " placeholder="请输入讲座开始时间范围"/>
                            </i-form-item>
                        </i-col>
                        <i-col span="6">
                            <i-form-item label="地点">
                                <i-input v-model="searchCondition.addr" placeholder="请输入讲座地点"/>
                            </i-form-item>
                        </i-col>
                        <i-col span="6"/>
                    </i-row>
                    <i-button type="primary" @click="getLecture()">搜索</i-button>
                    <i-button icon="ios-trash" @click="clearSearch()">清空条件</i-button>
                    <i-button type="text" @click="advanceSearch = !advanceSearch">{{advanceSearch ? "普通搜索" : "高级搜索"}}</i-button>
                </i-form>
            </div>
            <i-divider/>
            <i-row type="flex" justify="space-between" style="margin-bottom: 8px;">
                <i-col>
                    <i-row type="flex" align="middle">
                        <i-col>
                            <i-button icon="md-add" @click="showModal = true" type="primary" ghost>新建讲座</i-button>
                        </i-col>
                        <i-col>
                            <i-divider style="height: 1.8em; margin: auto 16px" type="vertical"></i-divider>
                        </i-col>
                        <i-col>
                            本学期起止时间:
                            <i-date-picker v-model="termRange" separator=" 至 " type="daterange" placeholder="请输入学期起止时间"/>
                            <Button type="primary" @click="saveTerm" :loading="savingTerm">保存</Button>
                        </i-col>
                    </i-row>
                </i-col>
                <i-col>
                    <div>
                        <i-button type="primary" icon="ios-cloud-upload-outline">导入列表</i-button>
                        <i-button type="primary" icon="ios-cloud-download-outline">导出列表</i-button>
                    </div>
                </i-col>
            </i-row>
            <i-row type="flex">
                <i-col span="24">
                    <i-table :loading="tableLoading" @on-row-click="tableClickHandler" stripe :columns="LecturesCol" :data="LectureData" @on-sort-change="setTableSort">
                        <template slot-scope="{row, index }" slot="ope">
                            <Tooltip content="修改删除讲座与查看子讲座列表" :delay="500">
                                <a class="btn" href="javascript:;" @click="tableClickHandler(row, index)">[详情]</a>
                            </Tooltip>
                            <Tooltip content="直接进入子讲座列表" :delay="500">
                                <a class="btn" href="javascript:;" @click="toDetail(row.ID)">[子讲座]</a>
                            </Tooltip>
                        </template>
                    </i-table>
                </i-col>
            </i-row>
            <i-row style="margin-top: 8px">
                <i-page :total="total" show-sizer show-total @on-change="getLecture($event, null)" @on-page-size-change="getLecture(null, $event)"/>
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
                <i-form-item>
                    <template slot="label">
                        讲座时间
                        <Tooltip content="包含所有子讲座的时间范围">
                            <Icon type="md-help-circle" color="#2db7f5" />
                        </Tooltip>
                    </template>
                    <i-date-picker v-model="lecture.time" type="daterange" style="width: 100%"/>
                </i-form-item>
                <i-form-item label="地点" prop="place">
                    <i-input v-model="lecture.place" />
                </i-form-item>
                <i-form-item>
                    <template slot="label">
                        预约时间
                        <Tooltip content="此时间段内讲座可被预约">
                            <Icon type="md-help-circle" color="#2db7f5"/>
                        </Tooltip>
                    </template>
                    <i-date-picker v-model="lecture.reservationTime" format="yyyy-MM-dd HH:mm" type="datetimerange" style="width: 100%"/>
                </i-form-item>
                <i-form-item label="可预约人数" prop="reservation">
                    <i-input-number v-model="lecture.reservation" style="width: 100%"/>
                </i-form-item>
            </i-form>
            <template slot="footer">
                <Button type="primary" :loading="savingLecture" @click="saveLecture">保存</Button>
                <Button type="default" @click="cancelLecture">取消</Button>
            </template>
        </i-modal>
        <i-drawer class="drawer" v-model="showDrawer" width="640">
            <p class="title">
                讲座信息{{loadingDrawer ? "（加载中）" : ""}}
            </p>
            <p class="sub-title">
                {{drawerData.lecture.Name}}&nbsp;
                <Tooltip content="修改讲座信息" :delay="500">
                    <Button shape="circle" :loading="loadingDrawer" icon="md-create" @click="modifyLecture(drawerData.lecture)"></Button>
                </Tooltip>
                <Button shape="circle" :loading="loadingDrawer" type="error" icon="md-trash" @click="deleteLecture(drawerData.lecture.ID)" style="float: right"></Button>
            </p>
            <i-row class="content">
                <i-col span="12">
                    汇报者：{{drawerData.lecture.Hoster}}
                </i-col>
                <i-col span="12">
                    地点：{{drawerData.lecture.Address}}
                </i-col>
            </i-row>
            <i-row class="content">
                <i-col span="12">
                    开始时间：{{drawerData.lecture.BeginOn}}
                </i-col>
                <i-col span="12">
                    结束时间：{{drawerData.lecture.EndOn}}
                </i-col>
            </i-row>
            <i-row class="content">
                <i-col span="12">
                    预约开始时间：{{drawerData.lecture.SignUpBegin}}
                </i-col>
                <i-col span="12">
                    预约结束时间：{{drawerData.lecture.SignUpEnd}}
                </i-col>
            </i-row>
            <i-row class="content">
                <i-col span="12">
                    期数：{{drawerData.lecture.Serial}}
                </i-col>
                <i-col span="12">
                    报名人数限制：{{drawerData.lecture.SignUpLimit}}
                </i-col>
            </i-row>
            <i-divider />
            <p class="sub-title">
                子讲座列表
            </p>
            <i-row>
                <i-col span="12" v-for="(sublecture, index) in drawerData.sublectures" :key="sublecture.ID">
                    <i-card class="sublecture" title="sublecture.Name" :to="{name: 'SubActivityManager', query: {id: drawerData.lecture.ID, index }}">
                        <div slot="title" class="card-title">
                            {{sublecture.Name}}
                        </div>
                        <div slot="extra">
                            <Tag v-if="sublecture.Status === '进行中'" color="magenta">{{sublecture.Status}}</Tag>
                            <Tag v-else-if="sublecture.Status === '未开始'" color="cyan">{{sublecture.Status}}</Tag>
                            <Tag v-else-if="sublecture.Status === '已结束'" color="orange">{{sublecture.Status}}</Tag>
                            <Tag v-else color="default">{{sublecture.Status}}</Tag>
                        </div>
                        <i-row type="flex" align="middle">
                            <i-col span="11">
                                <div class="date">{{timeFormatter(sublecture.BeginOn, "yyyy年MM月dd日")}}</div>
                                <div class="time">{{timeFormatter(sublecture.BeginOn, "hh:mm")}}</div>
                            </i-col>
                            <i-col span="2" style="font-size: 24px">
                                ~
                            </i-col>
                            <i-col span="11">
                                <div class="date">{{timeFormatter(sublecture.EndOn, "yyyy年MM月dd日")}}</div>
                                <div class="time">{{timeFormatter(sublecture.EndOn, "hh:mm")}}</div>
                            </i-col>
                        </i-row>
                    </i-card>
                </i-col>
            </i-row>
        </i-drawer>
    </div>
</template>

<script>
let _ = require("lodash");
const app = require("@/config")
const axios = require("axios");
export default {
    data () {
        return {
            drawerData: {
                lecture: {},
                sublectures: []
            },
            loadingDrawer: false,
            showDrawer: false,
            advanceSearch: false,
            autoCompleteOptions: [],
            termRange: [],
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
                    title: '汇报人',
                    key: 'Hoster'
                },
                {
                    title: '开始时间',
                    key: 'BeginOn',
                    sortable: 'custom'
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
                    title: '创建日期',
                    key: 'CreateOn',
                    sortable: 'custom'
                },
                {
                    title: '操作',
                    slot: 'ope'
                }
            ],
            LectureData: [],
            savingLecture: false,
            tableLoading: false,
            searchCondition: {},
            page: 1,
            pageSize: 10,
            total: 0,
            savingTerm: false
        }
    },
    created () {
        app.title = "讲座管理";
        this.getLecture();
        this.getTerm();
    },
    methods: {
        judgeSignInState (form, to) {
            let now = new Date();
            if (now < form) {
                return "未开始"; // 活动（签到）未开始
            } else if (now <= to) {
                return "签到中"; // 正在签到
            } else if (now > to) {
                return "已结束"; // 活动（签到）已经结束
            }
        },
        tableClickHandler (data, index) {
            this.loadingDrawer = true;
            axios.post("/api/activity/GetActivityCategory", {id: data.ID, order: "serial"}, msg => {
                this.loadingDrawer = false;
                if (msg.success) {
                    this.drawerData.lecture = msg.data;
                    this.drawerData.sublectures = msg.activities;
                } else {
                    this.$Message.error(msg.msg);
                }
            })
            this.showDrawer = true;
        },
        timeFormatter (date, fmt = "yyyy-MM-dd hh:mm:ss") {
            date = new Date(date);
            let o = {
                "M+": date.getMonth() + 1, // 月份
                "d+": date.getDate(), // 日
                "h+": date.getHours(), // 小时
                "m+": date.getMinutes(), // 分
                "s+": date.getSeconds(), // 秒
                "q+": Math.floor((date.getMonth() + 3) / 3), // 季度
                "S": date.getMilliseconds() // 毫秒
            };
            if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (date.getFullYear() + "").substr(4 - RegExp.$1.length));
            for (var k in o) {
                if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
            }
            return fmt;
        },
        deleteLecture (ID) {
            this.$Modal.confirm({
                title: "确认删除",
                content: "确实要删除本讲座吗？此操作不可恢复。",
                onOk: () => {
                    axios.post("/api/activity/RemoveActivityCategory", {id: ID}, msg => {
                        if (msg.success) {
                            this.$Message.success("删除成功");
                            this.showDrawer = false;
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
        setTableSort (e) {
            if (e.key === "BeginOn") {
                this.searchCondition.order = "begin";
            } else if (e.key === "CreateOn") {
                this.searchCondition.order = "create";
            }
            this.getLecture();
        },
        getLecture (targetPage, targetPageSize) {
            let page = targetPage || this.page;
            let pageSize = targetPageSize || this.pageSize;
            if (this.searchCondition.date && this.searchCondition.date[0]) {
                this.searchCondition.beginOn = this.searchCondition.date[0];
                this.searchCondition.endOn = this.searchCondition.date[1];
            } else {
                this.searchCondition.beginOn = undefined;
                this.searchCondition.endOn = undefined;
            }
            this.tableLoading = true;
            axios.post("/api/activity/GetAcitvities", {page, pageSize, ...this.searchCondition}, msg => {
                this.tableLoading = false;
                if (msg.success) {
                    this.LectureData = msg.data;
                    this.total = msg.totalRow;
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
                    axios.postStream("/api/activity/SaveActivityCategory", {
                        ID: this.lecture.id,
                        Name: this.lecture.title,
                        BeginOn: this.timeFormatter(this.lecture.time[0]),
                        EndOn: this.timeFormatter(this.lecture.time[1]),
                        SignUpBegin: this.timeFormatter(this.lecture.reservationTime[0]),
                        SignUpEnd: this.timeFormatter(this.lecture.reservationTime[1]),
                        Address: this.lecture.place,
                        Serial: this.lecture.count,
                        Hoster: this.lecture.reporter,
                        SignUpLimit: this.lecture.reservation
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
        saveTerm () {
            this.savingTerm = true;
            axios.post("/api/activity/SetTerm", {from: this.termRange[0], to: this.termRange[1]}, msg => {
                this.savingTerm = false;
                if (msg.success) {
                    this.$Message.success("保存成功");
                } else {
                    this.$Message.error(msg.msg);
                }
            })
        },
        getTerm () {
            axios.post("/api/activity/GetTerm", {}, msg => {
                this.termRange = [new Date(msg.from), new Date(msg.to)];
            })
        },
        modifyLecture (src) {
            this.lecture.id = src.ID;
            this.lecture.title = src.Name;
            this.lecture.time = [src.BeginOn, src.EndOn];
            this.lecture.reservationTime = [src.SignUpBegin, src.SignUpEnd];
            this.lecture.place = src.Address;
            this.lecture.count = src.Serial;
            this.lecture.reporter = src.Hoster;
            this.lecture.reservation = src.SignUpLimit;
            this.showModal = true;
        },
        clearSearch () {
            this.searchCondition = {};
            this.getLecture();
        },
        search (obj) {
            this.searchCondition[obj.category] = obj.title;
            this.getLecture();
        },
        calcSearchOptions (value) {
            if (value === "") {
                this.autoCompleteOptions = [];
                this.clearSearch();
                return;
            }

            let options = [];

            /* 是科研楼 */
            if (value.startsWith('科研')) {
                let category = "place";
                let tmp = {
                    title: "地点",
                    children: []
                }
                tmp.children.push({
                    title: "科研1",
                    category
                })
                tmp.children.push({
                    title: "科研2",
                    category
                })
                options.push(tmp);
            }

            /* 是教学楼 */
            if (value.startsWith('教学')) {
                let category = "place";
                let tmp = {
                    title: "地点",
                    children: []
                }
                tmp.children.push({
                    title: "教学楼",
                    category
                })
                options.push(tmp);
            }

            let tmp = {
                    title: "讲座名",
                    children: []
                }
                tmp.children.push({
                    title: value,
                    category: "name"
                });
            options.push(tmp);

            this.autoCompleteOptions = options;
        }
    }
}
</script>

<style scoped>
    .drawer a {
        color: #515a6e;
    }
    .drawer a:hover {
        color: #515a6e;
    }
    .drawer .title {
        font-size: 24px;
        color: #464c5b;
        margin-bottom: 16px;
    }
    .drawer .sub-title {
        font-size: 18px;
        color: #464c5b;
        margin-bottom: 16px;
    }
    .drawer .content {
        font-size: 16px;
        color: #657180;
        margin-bottom: 12px;
    }
    .drawer .sublecture {
        margin: 8px;
    }
    .drawer .card-title {
        line-height: 24px;
    }
    .drawer .time {
        font-size: 24px;
        font-weight: normal;
    }
    .drawer .date {
        font-size: 14px;
    }
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
