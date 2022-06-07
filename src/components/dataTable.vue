<template>
    <div class="d-grid gap10">
		<div class="d-flex">
	        <per-page @select="handlePerPageSelect"/>
			<input class="m-left-auto" type="text" v-model="searchKey" placeholder="Search employees">
		</div>
        <table id="employees">
            <thead>
                <tr>
                    <th v-for="(info, idx) in employeeInfoKeys" @click="handleSort(info)" :key="idx">
                        <div class="d-flex gap10">
							{{info.label}}
							<div class="pointer">
								<img src="../assets/icons/arrow-up.svg" :class="['icon', {'fadeIcon': (currSort.key === info.key ? !currSort.ascending : false)}]">
								<img src="../assets/icons/arrow-down.svg" :class="['icon', {'fadeIcon': (currSort.key === info.key ? currSort.ascending : false)}]">
							</div>
						</div>
                    </th>
                </tr>
            </thead>
            <tbody> 
                    <tr v-for="empl in sortEmployees" :key="empl.id">
                        <td v-for="info in employeeInfoKeys" :key="empl.id + info.key">
                            {{ empl[info.key] }}
                        </td>
                    </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th v-for="(info, idx) in employeeInfoKeys" @click="sortByKey(info.key)" :key="idx + 'f'">
                        {{info.label}}
                    </th>
                </tr>
            </tfoot>
        </table>
		<pagination :currentPage="currEmployeePage" 
					:perPage="currEmployeePerPage"
					@paginate="handlePagination" 
					:totalRows="totalEmployeeCount"/>
    </div>
</template>
<script>
import { mapState } from 'vuex';
import Pagination from './Pagination.vue';
import PerPage from './PerPage.vue';
export default {
  components: { PerPage, Pagination  },
    data(){
          return {
			searchKey: '',
            employeeData: [],
            empInfo: [],
            employeeInfoKeys: [
                {key: 'name', label: 'Name', dataType: 'string'},
                {key: 'position', label: 'Position', dataType: 'string'},
                {key: 'office', label: 'Office', dataType: 'string'},
                {key: 'extn', label: 'Extn.', dataType: 'number'},
                {key: 'start_date', label: 'Start date', dataType: 'date'},
                {key: 'salary', label: 'Salary', dataType: 'salary'}
            ],
			currEmployeePage: 1,
			currEmployeePerPage: 5,
			currSort: {key: '', ascending: null, type: ''}
        }
    },
	computed: {
		...mapState({
			employees: state => state.employees
		}),
		sortEmployees(){
			const {key, ascending, type} = this.currSort
			if(!key) return this.paginateEmployees;
			let sorter;
			if(type === 'string') {
				if(ascending){
					sorter = (a, b) => a[key].toLowerCase() > b[key].toLowerCase() ? 1 : -1; 
				} else {
					sorter = (a, b) => a[key].toLowerCase() < b[key].toLowerCase() ? 1 : -1; 
				}
			} else if (type === 'number'){
				if(ascending){
					sorter = (a, b) => a[key] - b[key]; 
				} else {
					sorter = (a, b) => b[key] - a[key]; 
				}
			} else if (type === 'salary'){
				let chars = {'$':'',',':''};
				sorter = (a, b) => {
					let intA = parseInt(a[key].replace(/[$,]/g, m => chars[m]));
					let intB = parseInt(b[key].replace(/[$,]/g, m => chars[m]));
					if(ascending){
						return intA - intB;
					} else {
					    return intB - intA;
				    }
				}	
			} else if (type === 'date'){
				sorter = (a,b) => {
					if(ascending){
						return new Date(a[key]) - new Date(b[key]);
					} else {
						return new Date(b[key]) - new Date(a[key]);
				    }
				}
			}
			return this.paginateEmployees.sort(sorter);
		},
		totalEmployeeCount(){
			return this.employees.length;
		},
		paginateEmployees(){
			return this.filteredEmployees.slice((this.currEmployeePage - 1) * this.currEmployeePerPage, this.currEmployeePage * this.currEmployeePerPage);
		},
		filteredEmployees(){
			const key = this.searchKey.toLowerCase();
			return this.employees.filter(empl => empl.name.toLowerCase().includes(key));
		}
  	},
	methods:{
		handlePerPageSelect(value){
			this.currEmployeePerPage = value;
		},
		handlePagination(value){
			this.currEmployeePage = value;
		},
		handleSort(value){
			const ascending = value.key === this.currSort.key ? !this.currSort.ascending : true;
			this.currSort = {
				key: value.key,
				ascending,
				type: value.dataType
			}
		}
	}
}
</script>

<style>
#employees {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

#employees td, #employees th {
  border: 1px solid #ddd;
  padding: 8px;
}

#employees tr:nth-child(even){background-color: #f2f2f2;}

#employees tr:hover {background-color: #ddd;}

#employees th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #04AA6D;
  color: white;
}

.fadeIcon {
	opacity: 0.2;
}
</style>