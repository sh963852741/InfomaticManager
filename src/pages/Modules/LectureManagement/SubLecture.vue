<template>
    <i-row>
        <i-col span="5">
            <Tree :data="data1" expand-node></Tree>
        </i-col>
        <i-col span="19">
            <i-card title="讲座详情">
            <Tabs value="name1">
                <TabPane label="详细信息" name="name1">标签一的内容</TabPane>
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
            ]
        }
    }
}
</script>

<style>

</style>
