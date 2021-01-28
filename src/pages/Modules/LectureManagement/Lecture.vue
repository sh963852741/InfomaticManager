<template>
    <i-row>
        <i-card title="讲座列表">
            <i-row>
                <i-row type="flex" :gutter="16">
                    <i-col>
                        <i-button @click="showModal = true" type="primary">新建讲座</i-button>
                    </i-col>
                    <i-col span="10">
                        <i-auto-complete icon="ios-search" @on-change="calcSearchOptions" @on-select="search">
                            <div class="demo-auto-complete-item" :key="item.title" v-for="item in autoCompleteOptions">
                                <div class="demo-auto-complete-group">
                                    <span>{{ item.title }}</span>
                                    <!-- <a href="https://www.google.com/search?q=iView" target="_blank">更多</a> -->
                                </div>
                                <Option v-for="option in item.children" :label="option.title" :value="option" :key="option.title">
                                    <span class="demo-auto-complete-title">{{ option.title }}</span>
                                    <!-- <span class="demo-auto-complete-count">{{ option.count }} 个结果</span> -->
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
                        <i-form-item label="期数">
                            <i-input v-model="searchCondition.serial" placeholder="请输入讲座期数" />
                        </i-form-item>
                    </i-col>
                    <i-col span="6">
                        <i-form-item label="题目">
                            <i-input v-model="searchCondition.name" placeholder="请输入讲座题目"/>
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
                            <i-date-picker style="width: 100%;" v-model="searchCondition.date" separator=" 至 " placeholder="请输入讲座开始时间范围"/>
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
                <i-divider />
            </i-form>
            <i-row type="flex" justify="space-between" :gutter="16" style="margin-bottom: 8px;">
                <i-col>
                    <i-form label-colon label-position="left" :label-width="130" inline>
                        <i-form-item label="本学期起止时间">
                            <i-date-picker v-model="termRange" separator=" 至 " type="daterange" placeholder="请输入学期起止时间"/>
                        </i-form-item>
                            <Button type="primary" @click="saveTerm" :loading="savingTerm">保存</Button>
                    </i-form>
                </i-col>
                <i-col>
                    <i-row>
                        <i-button type="primary" icon="ios-cloud-upload-outline">导入列表</i-button>
                        <i-button type="primary" icon="ios-cloud-download-outline">导出列表</i-button>
                    </i-row>
                </i-col>
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
    </i-row>
</template>

<script>
let _ = require("lodash");
const axios = require("axios");
export default {
    data () {
        return {
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
            searchCondition: {},
            page: 1,
            pageSize: 10,
            total: 0,
            savingTerm: false
        }
    },
    created () {
        this.getLecture();
        this.getTerm();
    },
    methods: {
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
        getLecture (targetPage, targetPageSize) {
            let page = targetPage || this.page;
            let pageSize = targetPageSize || this.pageSize;
            if (this.searchCondition.date && this.searchCondition.date[0]) {
                this.searchCondition.beginOn = this.searchCondition.date[0];
                this.searchCondition.endOn = this.searchCondition.date[1];
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
                        BeginOn: this.lecture.time[0],
                        EndOn: this.lecture.time[1],
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
