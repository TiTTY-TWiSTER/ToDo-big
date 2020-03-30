<template>
	<!-- <div>{{$route.params.id}}</div> -->
	<div class="container">

		<h1>{{req.Title}}</h1>
		<!-- <div v-for='item in req.description.split("*")'> -->
		<div v-for='(item,index) in save2'>
			<p>{{item}}</p>
			<span @click='del(index)'>x</span>
			<button v-show='item !=""' @click.prevent="post(item)">изменить</button>
		</div>

		<div v-show='changeV == true'>
			<input type="text" id="changeText" v-model='itemV' class="mt-5 form-control">
			<div class="mt-4">
				<button class="btn btn-primary">Сохранить</button>
				<button class="btn btn-secondary">отменить редактирование</button>
				<button class="btn btn-danger">удалить</button>
			</div>
		</div>

	</div>
</template>
<script>
import JQuery from 'jquery'
 	let $ = JQuery

	export default{
		async asyncData({params}){
			var id = params.id //получили динамический параметр от главной страницы

			var FormData = require('form-data'); //собрали данные для отправки через FormData
		    var obj = new FormData();
		    obj.append('id', id);

			var req = await fetch('https://maximum-movies.com/todo',{ //делаем POST запрос
	          method: 'post',
	          body:obj
		       }).then((res) => {
		            return res.json();
		        })
		        .then((data) => {
		          //console.log(data)
		            return data;
		        })
			return{req}
		},
		data(){
			return{
				itemV:'',
				changeV:false,
				save:[],
				save2:''
			}
		},
		mounted(){
			this.save = this.req.description.split('*')
			this.save2 = this.save.filter((item)=>{
				if(this.save[item] !=''){
					return item
				}
			})
			console.log(this.save2)
		},
		methods:{
			post(data){

				this.itemV = data

				this.changeV = !this.changeV

				if(this.changeV == true){//небольшой фикс, что бы после рендеринга сделать инпут активным
					setTimeout(()=>{
						$('input').focus();
					},5)
				}
			},
			del(data){
				this.save2.splice(data,1)
			}
		}
	}
</script>