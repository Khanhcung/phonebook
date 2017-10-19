<template>
<div>
	<nav class="panel column is-offset-2 is-8">
	  <p class="panel-heading">
	    Phone Book
	    <button class="button is-link is-outlined" @click="openAdd">
	      Add new
	    </button>
	    <span class="is-pulled-right" v-if="loading">
	    	<i class="fa fa-refresh fa-spin fa-2x fa-fw"></i>
	    </span>
	  </p>
	  <div class="panel-block">
	    <p class="control has-icons-left">
	      <input class="input is-small" type="text" placeholder="search" v-model="search">
	      <span class="icon is-small is-left">
	        <i class="fa fa-search"></i>
	      </span>
	    </p>
	  </div>
	  <a class="panel-block" v-for="item,key in temp">
	    <span class="column is-9">
	      {{ item.name }}
	    </span>
	    <span class="panel-icon column is-1">
	    	<i class="has-text-danger fa fa-trash" aria-hidden="true" @click="del(key,item.id)"></i>
	    </span>
	    <span class="panel-icon column is-1">
	    	<i class="has-text-info fa fa-edit" aria-hidden="true" @click="openEdit(key)"></i>
	    </span>
	    <span class="panel-icon column is-1">
	    	<i class="has-text-primary fa fa-eye" aria-hidden="true" @click="openShow(key)"></i>
	    </span>
	  </a>
	</nav>

<Add :openmodal='addActive' @closeRequest='close'></Add>
<Show :openmodal='showActive' @closeRequest='close'></Show>
<Edit :openmodal='editActive' @closeRequest='close'></Edit>	
</div>

</template>

<script>
let Add = require('./Add.vue');
let Show = require('./Show.vue');
let Edit = require('./Edit.vue');
	export default{
		components:{Add,Show,Edit},
		data(){
			return{
				addActive: '',
				showActive:'',
				editActive:'',
				list:{},
				errors:{},
				loading:false,
				search:'',
				temp:{}
			}
		},
		watch:{
			search(){
				if (this.search.length > 0) {
					this.temp = this.list.filter((item) => {
						 return Object.keys(item).some((key)=>{
							let string = String(item[key])
							return string.toLowerCase().indexOf(this.search.toLowerCase()) >-1
						})
						
					});
				} else{
					this.temp = this.list
				}
			}
		},
		mounted(){
			axios.post('/getData')
			.then((response)=> this.list = this.temp = response.data)
  			.catch((error) => this.errors = error.response.data.errors)
		},
		methods:{
			openAdd(){
				this.addActive = 'is-active';
				this.$children.list.name='';
			},
			close(){
				this.addActive = this.showActive = this.editActive = '';
			},
			openShow(key){
				this.$children[1].list = this.temp[key]
				this.showActive = 'is-active';
			},
			openEdit(key){
				this.$children[2].list = this.temp[key]
				this.editActive = 'is-active';
			},
			del(key,id){
				if (confirm("Are you sure?")) {
					axios.delete(`/phonebook/${id}`)
				       	 .then((response)=>this.list.splice(key,1))
				         .catch((error)=>this.errors = error.response.data.errors)
				}
			}
		}
	}

</script>