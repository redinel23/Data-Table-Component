<template>
    <div class="d-flex gap10">
        <label for="perPage">Show</label>
        <select name="perPage" id="perPage" @input="setPerPage">
            <option v-for="option in options"
                    :value="option" 
                    :key="option">
                        {{option}}
            </option>
        </select>
        <div>entries</div>
    </div>
</template>
<script>
    export default {
        props:{
            options: {
                type: Array,
                default: [5, 10, 20, 50]
            }
        },
        methods:{
            setPerPage(evt){
                const value = parseInt(evt.currentTarget.value);
                this.$emit('select', value);
            }
        }
    }
</script>
<style>

</style>
