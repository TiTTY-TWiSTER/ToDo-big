<template>
  <div class="container">
    <div v-for='item in all'>
		<h2>{{item.Title}}</h2>
		<p v-for='next in item.description.split("*")'>{{next}}</p>
		<!-- <p v-for='(point,index) in all2' v-if='item.id == index+1'>{{point}}</p> -->
		<button @click='change(item.id)'>Редактировать</button>
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
				all2:[]
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
			}
	}
}
</script>

<style>

</style>
