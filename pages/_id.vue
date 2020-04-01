<template>
	<!-- <div>{{$route.params.id}}</div> -->
	<div class="container">

		<div class="row">
			<h1>{{req.Title}}</h1>
			<button class="btn" @click.prevent="post(item,index)">Добавить</button>
		</div>
		<!-- <div v-for='item in req.description.split("*")'> здесь получаем из asyncData -->
		<div v-for='(item,index) in save2'> <!--а здесь нет; передаем индекс для удаления элемента-->
			<p>{{item}}</p>
			<button v-show='item !=""' @click.prevent="post(item,index)">изменить</button>
		</div>
	
		<div v-show='changeV == true'>
			<input type="text" id="changeText" v-model='itemV' class="mt-5 form-control">
			<div class="mt-4">
				<button class="btn btn-primary" @click='news'>Сохранить</button>
				<button class="btn btn-secondary" @click='status'>отменить редактирование</button>
				<button class="btn btn-danger" @click.prevent='del'>удалить</button>
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
				itemV:'',//отслеживание динамического параметра для input
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
			// for(var item = 0;item<this.save2.length;item++){
			// 	console.log(this.save2[item]+'*')
			// }
			console.log(this.save2)
		},
		methods:{
			post(data,index){
				localStorage.setItem('index',index) //нужно для удаления пункта ToDo по id
				this.itemV = data + '*'

				this.changeV = !this.changeV

				if(this.changeV == true){//небольшой фикс, что бы после рендеринга сделать инпут активным
					setTimeout(()=>{
						$('input').focus();
					},5)
				}
			},
			del(){
				let index = localStorage.getItem('index')
				this.save2.splice(index,1)
			},
			news(){
				var id = this.req.id
				var news = this.save2 //получили динамический параметр от главной страницы

				var FormData = require('form-data'); //собрали данные для отправки через FormData
			    var obj = new FormData();
			    obj.append('id', id);
			    obj.append('news', news);

			    var req = fetch('https://maximum-movies.com/todochange',{ //делаем POST запрос
		          method: 'post',
		          body:obj
			    }).then((data) => {
		          window.location.reload()
		            return data;
		        })
			    
			},
			status(){
				this.save2.push("*"+this.itemV)
				console.log(this.save2)
			}
		}
	}
</script>