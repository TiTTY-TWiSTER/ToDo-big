<template>
	<div>
		<button @click='RedText'>go</button>
		<section>
			<div v-for='item in all'>
				<h2>{{item.Title}}</h2>
				<p v-for='(point,index) in all2' v-if='item.id == index+1'>{{point}}</p>
			</div>

		</section>
	</div>
</template>
<script>
import JQuery from 'jquery'
 	let $ = JQuery
	export default{
		async asyncData({ $axios }) {
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
				//console.log(this.all)
				var arr = []
				for(var i = 0; i<this.all.length;i++){
					let goodChange = this.all[i].description.split(' ') //вырезаем пробелы
					let joy = goodChange.join(' ')//добавляем в одну строку без разделителя но с пробелами
					
					//console.log(joy)
					arr.push(joy.split('*')) //размечаем кол-во заметок по нашему разделителю			
				}
				console.log(arr)
				this.all2 = arr
			}
		}
	}
</script>