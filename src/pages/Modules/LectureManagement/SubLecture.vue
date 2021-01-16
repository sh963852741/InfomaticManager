<template>
    <i-row>
        <i-col span="4">
            <i-menu :active-name="activeMenu" width="auto" style="z-index: 8;" @on-select="getSubLecture">
                <i-menu-item name="New" style="border-bottom: 1px dashed #dcdee2;">
                    <Icon type="md-add-circle" color="#2d8cf0"/>
                    新建子讲座
                </i-menu-item>
                <!-- <i-menu-item name="2">光的散射</i-menu-item>
                <i-menu-item name="3">光的传播</i-menu-item>
                <i-menu-item name="4">光的色散</i-menu-item>
                <i-menu-item name="5">波粒二象性</i-menu-item> -->
                <i-menu-item v-for="tmp in subLectureData" :key= tmp.ID :name = tmp.ID>{{tmp.Name}}</i-menu-item>
            </i-menu>
        </i-col>
        <i-col span="20">
            <i-card dis-hover :bordered="false">
                <i-row type="flex">
                    <i-col span="1">
                        <i-avatar :src="app.webInfo.avatar" />
                    </i-col>
                    <i-col span="23">
                        <i-row style= "margin-bottom: 16px;" type="flex" justify="space-between" align="middle">
                            <i-col>
                                <span class="head-title">子讲座信息：{{subLecture.title ? subLecture.title : "&lt;请输入讲座题目&gt;"}}</span>
                            </i-col>
                            <i-col>
                                <i-button icon="md-create" type="primary" @click="saveSubLecture">{{addSubLectureMode ? "确认新建" : "确认修改"}}</i-button>
                                <i-button type="error" icon="md-trash" :disabled="addSubLectureMode || this.subLectureData.length <= 1" @click="deleteSubLecture">删除子讲座</i-button>
                            </i-col>
                        </i-row>
                        <i-row type="flex" justify="space-between">
                            <i-col span="18" id="sub-lecture-detail">
                                <i-form ref="subLectureForm" label-position="left" :label-width="120" :label-colon="true" :modal="subLecture">
                                    <i-row type="flex" justify="space-between">
                                        <i-col span="10">
                                            <i-form-item label="汇报题目" prop="title">
                                                <i-input size="small" v-model="subLecture.title" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="讲座期数">
                                                <i-input size="small" v-model="subLecture.count" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="汇报人姓名">
                                                <i-input size="small" v-model="subLecture.reporter" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="汇报时间">
                                                <i-date-picker size="small" v-model="subLecture.time" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="汇报地点">
                                                <i-input size="small" v-model="subLecture.place" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="预约开始时间">
                                                <i-date-picker size="small" v-model="subLecture.bookingBegin" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="预约结束时间">
                                                <i-date-picker size="small" v-model="subLecture.bookingEnd" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="10">
                                            <i-form-item label="可预约人数">
                                                <i-input size="small" v-model="subLecture.availableCount" />
                                            </i-form-item>
                                        </i-col>
                                    </i-row>
                                </i-form>
                            </i-col>
                            <i-col>
                                <p style="color: rgb(128, 134, 149);">状态</p>
                                <p style="font-size: 24px;">{{subLecture.status}}</p>
                            </i-col>
                        </i-row>
                    </i-col>
                </i-row>
                <i-tabs value="name2">
                    <i-tab-pane label="详细介绍" name="name1">
                        <ueditor :config="config" v-model="lecture.Content" style="width: 100%" />
                    </i-tab-pane>
                    <i-tab-pane label="报名管理" name="name2">
                        <i-row type="flex" justify="space-between">
                            <i-col>
                                <i-input search enter-button="搜索" placeholder="请输入要搜索的信息" />
                            </i-col>
                            <i-col>
                                <Button class="outPutBtn" type="primary">导出报名信息</Button>
                            </i-col>
                        </i-row>
                        <i-row class="table-margin">
                            <Table stripe :columns="signUpCol" :data="signUpData"></Table>
                        </i-row>
                    </i-tab-pane>
                    <i-tab-pane label="签到管理" name="name3">
                        <i-row type="flex" justify="space-between">
                            <i-col>
                                <i-input search enter-button="搜索" placeholder="请输入要搜索的信息" />
                            </i-col>
                            <i-col>
                                <Button class="outPutBtn" type="primary">导出签到信息</Button>
                            </i-col>
                        </i-row>
                        <i-row class="table-margin">
                            <Table stripe :columns="signInCol" :data="signInData"></Table>
                        </i-row>
                    </i-tab-pane>
                </i-tabs>
            </i-card>
        </i-col>
        <BackTop></BackTop>
    </i-row>
</template>

<script>
const app = require("@/config")
const axios = require("axios");
export default {
    data () {
        return {
            config: {
                ...app.ueditor,
                initialFrameHeight: 800
            },
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
            app,
            lecture: {},
            subLecture: {
                title: "",
                count: "",
                reporter: "",
                time: "",
                place: "",
                bookingBegin: "",
                bookingEnd: "",
                availableCount: "",
                id: ""
            },
            addSubLectureMode: true,
            menuLoading: false,
            savingSubLecture: false,
            subLectureData: [],
            lectureData: {},
            selected: "",
            activeMenu: "New"
        }
    },
    created () {
        this.getSubLectures(this.$route.query.id);
    },
    methods: {
        saveSubLecture () {
            let form = this.$refs["subLectureForm"];
            form.validate((valid) => {
                if (valid) {
                    this.savingSubLecture = true;
                    axios.post("/api/activity/SaveActivity", {
                        ID: this.subLecture.id,
                        Name: this.subLecture.title,
                        BeginOn: this.subLecture.time,
                        // EndOn: this.subLecture.endTime,
                        SignUpBegin: this.subLecture.bookingBegin,
                        SignUpEnd: this.subLecture.bookingEnd,
                        SignUpLimit: this.subLecture.availableCount,
                        Address: this.subLecture.place,
                        Serial: this.subLecture.count,
                        Hoster: this.subLecture.reporter,
                        CategoryId: this.$route.query.id
                    }, msg => {
                        this.savingLecture = false;
                        if (msg.success) {
                            this.$Message.success("保存成功");
                            this.showModal = false;
                            this.addSubLectureMode = false;
                            this.getSubLectures(this.$route.query.id);
                            this.activeMenu = this.subLectureData[0].ID;
                            this.getSubLecture(this.subLectureData[0].ID);
                        } else {
                            this.$Message.error(`${msg.msg}：${msg.errors}`);
                        }
                    })
                }
            })
            // location.reload();
        },
        getSubLectures (lectureId) {
            this.menuLoading = true;
            axios.post("/api/activity/GetActivityCategory", {id: lectureId}, msg => {
                this.menuLoading = false;
                if (msg.success) {
                    this.lectureData = msg.data;
                    this.subLectureData = msg.activities;
                } else {
                    this.$Message.error(msg.msg);
                }
            })
        },
        getSubLecture (lectureId) {
            // var lectureId = this.selected;
            // console.log("selected: " + lectureId);
            if (lectureId !== "New") {
                this.addSubLectureMode = false;
                var i = 0;
                for (i = 0; i < this.subLectureData.length; i++) {
                    if (this.subLectureData[i].ID === lectureId) {
                        this.subLecture.title = this.subLectureData[i].Name;
                        this.subLecture.count = this.subLectureData[i].Serial;
                        this.subLecture.reporter = this.subLectureData[i].Hoster;
                        this.subLecture.time = this.subLectureData[i].BeginOn;
                        this.subLecture.id = this.subLectureData[i].ID;
                        this.subLecture.bookingBegin = this.subLectureData[i].SignUpBegin;
                        this.subLecture.bookingEnd = this.subLectureData[i].SignUpEnd;
                        this.subLecture.availableCount = this.subLectureData[i].SignUpLimit;
                        this.subLecture.place = this.subLectureData[i].Address;
                        this.subLecture.status = this.subLectureData[i].Status;
                        break;
                    }
                }
            } else {
                this.addSubLectureMode = true;
                this.subLecture = {};
                this.signInData = [];
                this.signUpData = [];
            }
        },
        deleteSubLecture () {
            this.$Modal.confirm({
                title: "确认删除",
                content: "确实要删除本子活动吗？此操作不可恢复。",
                onOk: () => {
                    axios.post("/api/activity/RemoveActivity", {id: this.subLecture.id}, msg => {
                        if (msg.success) {
                            this.$Message.success("删除成功");
                            this.getSubLectures(this.$route.query.id);
                            // this.activeMenu = this.subLectureData[0].ID;
                            // this.getSubLecture(this.subLectureData[0].ID);
                            this.$router.go(0)
                        } else {
                            this.$Message.error(msg.msg);
                        }
                    })
                    // location.reload();
                }
            })
        }
    }
}
</script>

<style>
.table-margin {
    margin-top: 16px;
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
.add-sub-lecture {
    padding: 8px 24px;
    background: #fff;
    border-right: 1px solid #dcdee2;
}
</style>
