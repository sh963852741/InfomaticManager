<template>
    <i-row>
        <i-card title="讲座列表">
            <i-row>
                <i-row type="flex" :gutter="16">
                    <i-col>
                        <i-button>新建讲座</i-button>
                    </i-col>
                    <i-col span="10">
                        <i-auto-complete icon="ios-search">
                            <div class="demo-auto-complete-item" :key="item.title" v-for="item in autoCompleteOptions">
                                <div class="demo-auto-complete-group">
                                    <span>{{ item.title }}</span>
                                    <a href="https://www.google.com/search?q=iView" target="_blank">更多</a>
                                </div>
                                <Option v-for="option in item.children" :value="option.title" :key="option.title">
                                    <span class="demo-auto-complete-title">{{ option.title }}</span>
                                    <span class="demo-auto-complete-count">{{ option.count }} 人关注</span>
                                </Option>
                            </div>
                            <a href="https://www.google.com/search?q=iView" target="_blank" class="demo-auto-complete-more">查看所有结果</a>
                        </i-auto-complete>
                    </i-col>
                    <i-col>
                        <i-button type="text" @click="advanceSearch = !advanceSearch">{{advanceSearch ? "高级搜索" : "普通搜索"}}</i-button>
                    </i-col>
                </i-row>
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
            <i-row>
                <!-- 这里是表格 -->
            </i-row>
        </i-card>
    </i-row>
</template>

<script>
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
            ]
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
