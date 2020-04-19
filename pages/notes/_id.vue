<template>
	<!-- <div>{{$route.params.id}}</div> -->
	<div class="container">
		<h1>{{req.Title}}</h1>
		<!-- <div v-for='item in req.description.split("*")'> здесь получаем из asyncData -->
		<div v-for='(item,index) in save2'> <!--а здесь нет; передаем индекс для удаления элемента-->
			<button class="btn btn-primary" @click.prevent="changeV = !changeV" v-show='index == 0'>Добавить</button>
			<p>{{item | trim}}</p>
			<button @click.prevent="post(item,index)">изменить</button>			
		</div>
	
		<div v-show='changeV == true'>
			<input type="text" id="changeText" v-model='itemV' class="mt-5 form-control">
			<div class="mt-4">
				<button class="btn btn-primary" @click='news'>Сохранить</button>
				<button class="btn btn-secondary" @click='changeV = false'>отменить редактирование</button>
				<button class="btn btn-danger" @click.prevent='del'>удалить</button>
			</div>
		</div>

	</div>
</template>
<script>
import JQuery from 'jquery'
 	let $ = JQuery

	export default{
		async asyncData({params,query}){

			var id = params.id //получили динамический параметр от главной страницы
			
			var user = query.token

			var FormData = require('form-data'); //собрали данные для отправки через FormData
		    var obj = new FormData();
		    obj.append('idFor', id);
		    obj.append('token2', user);		    

			let req = await fetch('https://maximum-movies.com/todo',{ //делаем POST запрос
	          method: 'post',
	          body:obj
		       }).then((res) => {
		            return res.json();
		        })
		        .then((data) => {
		          //console.log(data)
		            return data;
		        })
			return{req,user}
		},
		data(){
			return{
				itemV:'',//отслеживание динамического параметра для input
				changeV:false,
				save:[],
				save2:'',
				copy:[]
			}
		},
		filters:{
			trim:function(item){
				return item.trim()
			}
		},
		mounted(){
			console.log(this.req)
			this.save = this.req.description.split('*')
			this.save2 =  this.save.filter((item)=>{
				if(item != " " ){
					return item
				}
			})
			//console.log(this.save2)
			for(var count = 0;count<this.save2.length;count++){
				this.copy.push(this.save2[count]+'*')				
			}
			//console.log(this.copy)
		},
		methods:{
			
			post(data,index){
				localStorage.setItem('index',index) //нужно для удаления пункта ToDo по id
				
				this.itemV = data.trim() // для того что бы в input сразу отобразился редактируемый текст
				
				this.changeV = !this.changeV

				if(this.changeV == true){//небольшой фикс, что бы после рендеринга сделать инпут активным
					setTimeout(()=>{
						$('input').focus();
					},5)
				}
			},
			del(){
				this.itemV = ''// очищаем инпут
				let index = localStorage.getItem('index')
				this.save2.splice(index,1)
				this.copy.splice(index,1)
			},
			async news(){
				var id = this.req.id //получили динамический параметр от главной страницы
				var user = this.user
				this.copy.push(this.itemV + '*') // пушим только что написанное в input + разделитель
				var news = this.copy.join(" ")  // всё в строку что бы занимать лишь один столбец в БД

				var FormData = require('form-data'); //собрали данные для отправки через FormData
			    var obj = new FormData();
			    obj.append('id', id);
			    obj.append('news', news);
			    obj.append('token',user)

			    var req2 = await fetch('https://maximum-movies.com/todochange',{ //делаем POST запрос
		          method: 'post',
		          body:obj
			    }).then((res) => {
		            return res.json();
		        })
		        .then((data)=>{
		        	//window.location.reload()
		        	return data;
		        })
		        this.save2 = req2[0].description.split('*').filter((item)=>{
		        	if(item != " "){
		        		return item;
		        	}
		        });
		        this.changeV = false;
			},
		}
	}
</script>