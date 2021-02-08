<template>
    <i-row>
        <i-col span="4">
            <i-menu ref="menu" :active-name="activeMenu" width="auto" style="z-index: 8;" @on-select="getSubLecture">
                <i-menu-item name="New" style="border-bottom: 1px dashed #dcdee2;">
                    <Icon type="md-add-circle" color="#2d8cf0"/>
                    新建子讲座
                </i-menu-item>
                <i-menu-item v-for="(tmp, index) in subLectureData" :key="tmp.ID" :name="index">{{tmp.Name}}</i-menu-item>
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
                                <i-button icon="ios-arrow-back" @click="$router.back()" style="margin-left: 16px">返回父讲座</i-button>
                            </i-col>
                            <i-col>
                                <i-button icon="md-create" type="primary" :loading="savingSubLecture" @click="saveSubLecture">{{addSubLectureMode ? "确认新建" : "确认修改"}}</i-button>
                                <i-button type="error" icon="md-trash" :disabled="addSubLectureMode || this.subLectureData.length <= 1" @click="deleteSubLecture">删除子讲座</i-button>
                            </i-col>
                        </i-row>
                        <i-row type="flex" justify="space-between">
                            <i-col span="18" id="sub-lecture-detail">
                                <i-form ref="subLectureForm" label-position="left" :label-width="110" label-colon :modal="subLecture">
                                    <i-row type="flex" justify="space-between">
                                        <i-col span="11">
                                            <i-form-item label="汇报题目" prop="title">
                                                <i-input size="small" v-model="subLecture.title" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="11">
                                            <i-form-item label="讲座期数">
                                                <template slot="label">
                                                    讲座期数
                                                    <Tooltip content="若为数字则子讲座按此排序">
                                                        <Icon type="md-help-circle" color="#2db7f5" />
                                                    </Tooltip>
                                                    :
                                                </template>
                                                <i-input size="small" v-model="subLecture.count" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="11">
                                            <i-form-item label="汇报人姓名">
                                                <i-input size="small" v-model="subLecture.reporter" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="11">
                                            <i-form-item label="汇报地点">
                                                <i-input size="small" v-model="subLecture.place" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="11">
                                            <i-form-item label="开始时间">
                                                <template slot="label">
                                                    开始时间
                                                    <Tooltip content="此时间后可以签到">
                                                        <Icon type="md-help-circle" color="#2db7f5" />
                                                    </Tooltip>
                                                    :
                                                </template>
                                                <i-date-picker style="width: 100%;" size="small" type="datetime" v-model="subLecture.beginOn" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="11">
                                            <i-form-item label="结束时间">
                                                <i-date-picker style="width: 100%;" size="small" type="datetime" v-model="subLecture.endOn" />
                                            </i-form-item>
                                        </i-col>
                                        <!-- <Divider dashed style="margin: 0px 0px 16px 0px" />
                                        <i-col span="11">
                                            <i-form-item label="预约开始时间">
                                                <i-date-picker style="width: 100%;" size="small" type="datetime" v-model="subLecture.bookingBegin" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="11">
                                            <i-form-item label="预约结束时间">
                                                <i-date-picker style="width: 100%;" size="small" type="datetime" v-model="subLecture.bookingEnd" />
                                            </i-form-item>
                                        </i-col>
                                        <i-col span="11">
                                            <i-form-item label="可预约人数">
                                                <i-input size="small" v-model="subLecture.availableCount" />
                                            </i-form-item>
                                        </i-col> -->
                                    </i-row>
                                </i-form>
                            </i-col>
                            <i-col style="width: 100px">
                                <i-row>
                                    <p style="color: rgb(128, 134, 149);">状态</p>
                                    <p style="font-size: 24px;">{{subLecture.status}}</p>
                                </i-row>
                                <i-row style="bottom: 0px; position: absolute;">
                                    <Tooltip placement="left">
                                        <div id="qrcode"></div>
                                        <div slot="content" class="url_content">
                                            {{qrCodeUrl}}
                                        </div>
                                    </Tooltip >
                                </i-row>
                            </i-col>
                        </i-row>
                    </i-col>
                </i-row>
                <i-tabs value="name2">
                    <i-tab-pane label="详细介绍" name="name1">
                        <Alert>此处为母讲座的介绍</Alert>
                        <i-button @click="saveIntroduction" style="margin-bottom: 8px" type="primary">保存</i-button>
                        <ueditor :config="config" v-model="lectureData.Content" style="width: 100%" />
                        <i-button @click="saveIntroduction" style="margin-top: 8px" type="primary">保存</i-button>
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
                        <div class="table-margin">
                            <Table stripe :columns="signUpCol" :data="signUpData">
                                <template slot="State" slot-scope="{row}">
                                    {{signUpStateDic[row.State]}}
                                </template>
                            </Table>
                        </div>
                        <i-page style="margin-top: 8px" :total="pageData.signUp.total" show-sizer show-total @on-change="getLectureSignUp(null, $event, null)" @on-page-size-change="getLectureSignUp(null, null, $event)"/>
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
                        <div class="table-margin">
                            <Table stripe :columns="signInCol" :data="signInData"></Table>
                        </div>
                        <i-page style="margin-top: 8px" :total="pageData.signIn.total" show-sizer show-total @on-change="getSubLectureSingIn(null, $event, null)" @on-page-size-change="getSubLectureSingIn(null, null, $event)" />
                    </i-tab-pane>
                </i-tabs>
            </i-card>
        </i-col>
        <BackTop></BackTop>
    </i-row>
</template>

<script>
import QRCode from 'qrcodejs2'
const app = require("@/config")
const axios = require("axios");
const urlPrefix = window.location.protocol + "//" + window.location.host + "/mob/";
export default {
    data () {
        return {
            signUpStateDic: {
                999: "取消报名",
                0: "已报名"
            },
            config: {
                ...app.ueditor,
                initialFrameHeight: 600
            },
            signUpCol: [
                {
                    title: '姓名',
                    key: 'RealName'
                },
                {
                    title: '手机号',
                    key: 'Mobile'
                },
                {
                    title: '报名时间',
                    key: 'SignUpOn'
                },
                {
                    title: '邮箱',
                    key: 'Email'
                },
                {
                    title: '状态',
                    slot: 'State'
                }
            ],
            signUpData: [],
            signInCol: [
                {
                    title: '姓名',
                    key: 'RealName'
                },
                {
                    title: '学号',
                    key: 'Code'
                },
                {
                    title: '手机号',
                    key: 'Mobile'
                },
                {
                    title: '邮箱',
                    key: 'Email'
                },
                {
                    title: '签到时间',
                    key: 'SignInOn'
                }
            ],
            app,
            subLecture: {
                status: "未知",
                title: "",
                count: "",
                reporter: "",
                beginOn: new Date(),
                endOn: new Date(),
                place: "",
                bookingBegin: new Date(),
                bookingEnd: new Date(),
                availableCount: "",
                id: ""
            },
            addSubLectureMode: true,
            menuLoading: false,
            savingSubLecture: false,
            subLectureData: [],
            lectureData: {},
            selected: "",
            activeMenu: "New",
            signInData: [],
            qrcode: {},
            qrCodeUrl: "",
            pageData: {
                signUp: {
                    total: 0,
                    page: 1,
                    pageSize: 10
                },
                signIn: {
                    total: 0,
                    page: 1,
                    pageSize: 10
                }
            },
            lectureId: ""
        }
    },
    created () {
        app.title = "子讲座列表";
        this.lectureId = this.$route.query.id;
        this.activeMenu = this.$route.query.index;
    },
    mounted () {
        this.qrcode = new QRCode('qrcode', {
            width: 100, // 图像宽度
            height: 100, // 图像高度
            colorDark: "#000000", // 前景色
            colorLight: "#ffffff", // 背景色
            correctLevel: QRCode.CorrectLevel.H // 容错级别 容错级别有：（1）QRCode.CorrectLevel.L （2）QRCode.CorrectLevel.M （3）QRCode.CorrectLevel.Q （4）QRCode.CorrectLevel.H
        })
        this.getSubLectures(this.lectureId);
    },
    methods: {
        /* 获取报名状态 */
        getLectureSignUp (lectureId, targetPage, targetPageSize) {
            lectureId = lectureId || this.lectureId;
            let page = targetPage || this.pageData.signIn.page;
            let pageSize = targetPageSize || this.pageData.signIn.pageSize;
            axios.post("/api/activity/GetSignUps", {id: lectureId, page, pageSize}, msg => {
                if (msg.success) {
                    this.signUpData = msg.data;
                    this.pageData.signUp.total = msg.totalRow;
                    this.pageData.signUp.page = msg.page;
                    this.pageData.signUp.pageSize = msg.pageSize;
                } else {
                    this.$Message.error(msg.msg);
                }
            })
        },
        saveSubLecture () {
            let form = this.$refs["subLectureForm"];
            form.validate((valid) => {
                if (valid) {
                    this.savingSubLecture = true;
                    axios.postStream("/api/activity/SaveActivity", {
                        ID: this.subLecture.id,
                        Name: this.subLecture.title,
                        BeginOn: this.timeToString(this.subLecture.beginOn),
                        EndOn: this.timeToString(this.subLecture.endOn),
                        SignUpBegin: this.lectureData.SignUpBegin,
                        SignUpEnd: this.lectureData.SignUpEnd,
                        SignUpLimit: this.lectureData.SignUpLimit,
                        Address: this.subLecture.place,
                        Serial: this.subLecture.count,
                        Hoster: this.subLecture.reporter,
                        CategoryId: this.$route.query.id
                    }, msg => {
                        this.savingSubLecture = false;
                        if (msg.success) {
                            this.$Message.success("保存成功");
                            this.showModal = false;
                            this.addSubLectureMode = false;
                            this.getSubLectures(this.$route.query.id);
                        } else {
                            this.$Message.error(`${msg.msg}：${msg.errors}`);
                        }
                    })
                }
            })
        },
        saveIntroduction () {
            axios.postStream("/api/activity/SaveActivityCategory", this.lectureData, msg => {
                if (msg.success) {
                    this.$Message.success("已保存母讲座简介");
                } else {
                    this.$Message.error(msg.msg);
                }
            })
        },
        getSubLectures (lectureId) {
            this.menuLoading = true;
            this.$Spin.show();
            axios.post("/api/activity/GetActivityCategory", {id: lectureId, order: "serial"}, msg => {
                this.$Spin.hide();
                this.menuLoading = false;
                if (msg.success) {
                    this.lectureData = msg.data;
                    this.subLectureData = msg.activities;
                    this.getLectureSignUp(lectureId);
                    // 设置当前选中的菜单项，注意在$nextTick中调用
                    if (this.subLectureData.length) {
                        let index = typeof this.activeMenu === "number" ? this.activeMenu : 0;
                        this.activeMenu = index;
                        this.getSubLecture(index);
                        let menu = this.$refs["menu"];
                        this.$nextTick(() => {
                            menu.updateActiveName(index);
                        })
                    }
                } else {
                    this.$Message.error(msg.msg);
                }
            })
        },
        qrCode (url) {
            this.qrcode.clear(); // 清除二维码
            this.qrcode.makeCode(urlPrefix + url); // 生成另一个新的二维码
            this.qrCodeUrl = urlPrefix + url;
        },
        getSubLecture (index) {
            if (typeof index === "number") {
                this.addSubLectureMode = false;

                this.subLecture.title = this.subLectureData[index].Name;
                this.subLecture.count = this.subLectureData[index].Serial;
                this.subLecture.reporter = this.subLectureData[index].Hoster;
                this.subLecture.beginOn = new Date(this.subLectureData[index].BeginOn.replace(/-/g, '/'));
                this.subLecture.endOn = new Date(this.subLectureData[index].EndOn.replace(/-/g, '/'));
                this.subLecture.id = this.subLectureData[index].ID;
                this.subLecture.bookingBegin = new Date(this.subLectureData[index].SignUpBegin.replace(/-/g, '/'));
                this.subLecture.bookingEnd = new Date(this.subLectureData[index].SignUpEnd.replace(/-/g, '/'));
                this.subLecture.availableCount = this.subLectureData[index].SignUpLimit;
                this.subLecture.place = this.subLectureData[index].Address;
                this.subLecture.status = this.subLectureData[index].Status;

                this.qrCode(`iuc/signIn/signIn?id=${this.subLectureData[index].ID}`);

                this.getSubLectureSingIn(this.subLecture.id);
            } else if (index === "New") {
                this.addSubLectureMode = true;
                this.subLecture = {};
                this.subLecture.status = "未知";
                this.signInData = [];
                this.signUpData = [];
            }
        },
        getSubLectureSingIn (subLectureId, targetPage, targetPageSize) {
            subLectureId = subLectureId || this.subLecture.id;
            let page = targetPage || this.pageData.signIn.page;
            let pageSize = targetPageSize || this.pageData.signIn.pageSize;
            axios.post("/api/activity/GetSignIns", {id: subLectureId, page, pageSize}, msg => {
                if (msg.success) {
                    this.signInData = msg.data;
                    this.pageData.signIn.total = msg.totalRow;
                    this.pageData.signIn.page = msg.page;
                    this.pageData.signIn.pageSize = msg.pageSize;
                } else {
                    this.$Message.error(msg.msg);
                }
            })
        },
        deleteSubLecture () {
            this.$Modal.confirm({
                title: "确认删除",
                content: "确实要删除本子活动吗？此操作不可恢复。",
                onOk: () => {
                    axios.post("/api/activity/RemoveActivity", {id: this.subLecture.id}, msg => {
                        if (msg.success) {
                            this.$Message.success("删除成功");
                            this.activeMenu = 0;
                            this.getSubLectures(this.$route.query.id);
                        } else {
                            this.$Message.error(msg.msg);
                        }
                    })
                }
            })
        },
        timeToString (date) {
            if (!date) return;
            // return date.getFullYear() + '-' + (date.getMonth() + 1) + '-' + date.getDate() + ' ' + date.getHours() + ':' + date.getMinutes() + ':' + date.getSeconds();
            var y = date.getFullYear();
            var m = date.getMonth() + 1;
            var d = date.getDate();
            var hh = date.getHours();
            var mm = date.getMinutes();
            var ss = date.getSeconds();
            var str = '' + y;
            if (m < 10) {
                str = str + '-0' + m;
            } else {
                str = str + '-' + m;
            }
            if (d < 10) {
                str = str + '-0' + d;
            } else {
                str = str + '-' + d;
            }
            if (hh < 10) {
                str = str + '\u0020' + '0' + hh;
            } else {
                str = str + '\u0020' + hh;
            }
            if (mm < 10) {
                str = str + ':0' + mm;
            } else {
                str = str + ':' + mm;
            }
            if (ss < 10) {
                str = str + ':0' + ss;
            } else {
                str = str + ':' + ss;
            }
            str = str + '';
            return str;
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
.url_content {
    white-space: normal;
    word-break: break-all;
}
</style>
