<template>
  <div class="container">
  	<button class="d-block btn btn-primary mx-auto" @click='added' >Добавить новую</button>
    <div class="row align-items-center">
    	<div class="col-md-6">
    		<div v-for='item in all'>
				<h2>{{item.Title}}</h2>
				<p v-for='next in item.description.split("*")'>{{next}}</p>
				<!-- <p v-for='(point,index) in all2' v-if='item.id == index+1'>{{point}}</p> -->
				<button @click='change(item.id)'>Редактировать</button>
			</div>
    	</div>
    	<div class="col-md-6" v-show='add'>
    		<input type="text" class="form-control" placeholder="Название" v-model='title'>
    		<div id="place">
    			<input type="text" class="form-control mt-2" v-show='title!=""' placeholder="Добавить пункт" v-model='points'>    						
    		</div>
    		<button class="btn btn-success mt-2" v-show='title!=""' @click='more'>Еще</button>
    		
    		<button class="btn btn-info mt-2" @click='proove'>status</button>
    	</div>
    </div>
    <div class="container mt-3">
    	<h3>{{title}}</h3>
    	<ul>
    		<div v-for='(li,index) in copy'>
    			<li>{{li}}</li>
    			<button type="button" class="close" aria-label="Close" @click='close(index)'>
				  <span aria-hidden="true">&times;</span>
				</button>
    		</div>
    	</ul>
    	<button class="btn btn-dark mt-2" v-show='copy.length > 0'>Сохранить</button>
    </div>
  </div>
</template>

<script>

import JQuery from 'jquery'
 	let $ = JQuery

export default {
	async asyncData({ $axios }){
		const all = await $axios.$get('https://maximum-movies.com/todo')
		return { all }
	},
	data(){
			return{
				add:false,
				title:'',
				points:'',
				copy:[],
			}
		},
	mounted(){

		},
	components: {
        	
        },
	methods:{
			change(id){
				this.$router.push('/'+ id)
			},
			added(){
				this.add = !this.add
			},
			more(){
				this.copy.push(this.points)
				this.points = ''
				console.log(this.copy)
				//$('#place').append("<input type='text' class='form-control' placeholder='Название'>")
			},
			close(index){
				this.copy.splice(index,1)
			},
			proove(){
				console.log(this.copy)
				var stinks = $('#place').find('input')
				for(var ass=0;ass<stinks.length;ass++){
					console.log(stinks[ass].value)
				}
				//this.copy.push(this.points)
				//console.log(stinks)
			}
	}
}
</script>

<style>

</style>
