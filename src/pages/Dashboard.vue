<template>
    <div id="dashboard">
        <i-input maxlength="10" show-word-limit v-model="sample" placeholder="Enter something..." />
        <p>
            {{ sample }}
        </p>
        <input :value="sample" @input="input" />
    </div>
</template>

<script>
export default {
    methods: {
        input (e) {
            this.sample = e.target.value;
        }
    },
    data () {
        return {
            sample: ""
        };
    }
}
</script>

<style>
#dashboard input {
    width: 500px;
}
</style>
