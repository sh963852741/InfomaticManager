<template>
    <Dropdown>
        <Badge :count="notices.length" :offset="[12, 5]">
            <Icon :style="[{margin: '0 5px'},{cursor: 'pointer'}]" type="md-notifications-outline" size="24"></Icon>
        </Badge>
        <!-- <CellGroup slot="list">
            <Cell v-for="notice in notices" :key="notice.title" :title="notice.title" />
        </CellGroup>
        <a slot="list">全部已读</a> -->
        <DropdownItem class="dropItem" slot="list" v-for="notice in notices" :key="notice.title">
            <!-- <Icon :type="notice.icon"></Icon>
            <span class="innerSpan">{{notice.title}}</span> -->
            <List>
                <ListItem>
                    <ListItemMeta avatar="https://dev-file.iviewui.com/userinfoPDvn9gKWYihR24SpgC319vXY8qniCqj4/avatar" class="innerSpan" :title="notice.title" :description="notice.time" />
                </ListItem>
                <!-- <Divider /> -->
            </List>
        </DropdownItem>
        <DropdownItem class="dropItem" slot="list" style="text-align: center;" divided ><p @click="clearNotice">全部已读</p></DropdownItem>
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
.dropItem {
    width: 300px;
    min-height: 30px;
    max-width: 300px;
    vertical-align: middle;
    padding-top: 15px;
    padding-bottom: 15px;
    height: auto;
    word-wrap:break-word;
    word-break:break-all;
}

.innerSpan {
    overflow-wrap: break-word;
    word-wrap: break-word;
    word-break: break-all;
    word-break: break-word;
    hyphens: auto;
    white-space: normal;
    width: 250px;
}
</style>
