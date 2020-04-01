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
    	</div>
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
				all2:[],
				add:false,
				title:'',
				points:'',
				copy:[]
			}
		},
	mounted(){
			this.RedText()
		},
	methods:{
		RedText(){      
				var arr = []
				for(var i = 0; i<this.all.length;i++){
					let goodChange = this.all[i].description.split('*') //размечаем кол-во заметок по нашему разделителю
					arr.push(goodChange) 			
				}
				this.all2 = arr
				this.result = []
				for(var num = 0;num<this.all2.length;num++){
					for(var f = 0;f<this.all2[num].length;f++){
						this.result.push(this.all2[num][f])
					}
					console.log(this.result)
				}
			},
			change(id){
				this.$router.push('/'+ id)
			},
			added(){
				this.add = !this.add
			},
			more(){
				this.copy.push(this.points)
				$('#place').append('<input type="text" class="form-control mt-2" placeholder="Добавить пункт" v-model="points">')
			}
	}
}
</script>

<style>

</style>
