<template>
    <Dropdown>
        <Badge :count="notices.length" :offset="[12, 5]">
            <Icon :style="[{margin: '0 5px'},{cursor: 'pointer'}]" type="md-notifications-outline" size="24"></Icon>
        </Badge>
        <!-- <CellGroup slot="list">
            <Cell v-for="notice in notices" :key="notice.title" :title="notice.title" />
        </CellGroup>
        <a slot="list">全部已读</a> -->
        <DropdownItem slot="list" v-for="notice in notices" :key="notice.title">
            <Icon :type="notice.icon"></Icon>
            {{notice.title}}
        </DropdownItem>
        <DropdownItem slot="list" style="text-align: center;" divided ><p @click="clearNotice">全部已读</p></DropdownItem>
    </Dropdown>
</template>
<script>
export default {
    props: {
        notices: {
            type: Array,
            required: true
        }
    },
    data () {
        return {
            InternalNotices: []
        }
    },
    watch: {
        notices (v) {
            this.InternalNotices = v;
        }
    },
    methods: {
        clearNotice () {
            // console.log("clear");
            this.$emit("clear");
        }
    }
}
</script>
<style>
</style>
