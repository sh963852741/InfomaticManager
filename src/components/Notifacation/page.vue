<template>
    <i-row>
        <i-tabs>
            <i-tab-pane label="通知" name="notification">
                <List>
                    <ListItem>
                        <ListItemMeta avatar="https://dev-file.iviewui.com/userinfoPDvn9gKWYihR24SpgC319vXY8qniCqj4/avatar" title="This is title" description="This is description, this is description." />
                    </ListItem>
                    <ListItem>
                        <ListItemMeta avatar="https://dev-file.iviewui.com/userinfoPDvn9gKWYihR24SpgC319vXY8qniCqj4/avatar" title="This is title" description="This is description, this is description." />
                    </ListItem>
                    <ListItem>
                        <ListItemMeta avatar="https://dev-file.iviewui.com/userinfoPDvn9gKWYihR24SpgC319vXY8qniCqj4/avatar" title="This is title" description="This is description, this is description." />
                        <template slot="action">
                            <li>
                                <a href="">Edit</a>
                            </li>
                            <li>
                                <a href="">More</a>
                            </li>
                        </template>
                    </ListItem>
                    <ListItem>
                        <ListItemMeta avatar="https://dev-file.iviewui.com/userinfoPDvn9gKWYihR24SpgC319vXY8qniCqj4/avatar" title="This is title" description="This is description, this is description." />
                        <template slot="action">
                            <li>
                                <a href="">Edit</a>
                            </li>
                            <li>
                                <a href="">More</a>
                            </li>
                        </template>
                    </ListItem>
                </List>
            </i-tab-pane>
            <i-tab-pane label="关注" name="subscribe"></i-tab-pane>
            <i-tab-pane label="待办" name="todo"></i-tab-pane>
        </i-tabs>
    </i-row>
</template>

<script>
export default {

}
</script>

<style>

</style>