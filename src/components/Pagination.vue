<template>
    <div class="d-flex gap10">
        <button type="button" class="pointer" @click="prevNext(false)" :disabled="disablePrev">Previous</button>
        <div v-for="page in currentPages" 
            :key="page" 
            :class="['paginationNo pointer alignCenter border', {'selected': (currentPage === page)}, {'disabled': checkDisable(page)}]" 
            @click="paginate(page)">
                {{page}}
        </div>
        <button type="button" class="pointer" @click="prevNext(true)" :disabled="disableNext">Next</button>
    </div>
</template>

<script>
export default {
    props:{
        totalRows:{
            type: Number,
            default: 0
        },
        currentPage:{
            type: Number,
            default: 1
        },
        perPage:{
            type: Number,
            default: 1
        }
    },
    watch:{
       perPage(){
           this.paginate(1); // everytime per page changes, pagination should reset to 1 in case there are no records at the current page
       } 
    },
    computed:{
        currRecords(){
            return this.currentPage * this.perPage;
        },
        currentPages(){
            if(this.currentPage > 3){
                let pages = [
                    this.currentPage - 2,
                    this.currentPage - 1,
                    this.currentPage,
                ];
                if(this.currRecords < this.totalRows){
                    pages.push(this.currentPage + 1);
                }
                return pages;
            }
            return [1, 2, 3, 4];
        },
        disableNext(){
            return this.totalRows <= this.currRecords
        },
        disablePrev(){
            return this.currentPage === 1;
        }
    },
    methods:{
        paginate(value){
            this.$emit('paginate', value);
        },
        prevNext(next){
            if(next){
                this.paginate(this.currentPage + 1);
            } else {
                this.paginate(this.currentPage - 1);
            }
        },
        checkDisable(page){
            return this.totalRows <= ((page - 1) * this.perPage);
        }
    }

}
</script>

<style scoped>
.paginationNo{
    min-width: 20px;
    background-color: azure;
}
.selected{
    background-color: cadetblue;
}

.disabled{
    opacity: 0.5;
    cursor: default;
    pointer-events: none;
}
</style>