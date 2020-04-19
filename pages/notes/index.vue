<template>
  <div class="container">
  	<button class="d-block btn btn-primary mx-auto" @click='added' id="added_btn">Добавить новую</button>
    <div class="align-items-center mt-5">
    	<div id="all__notes">
    		<div v-for='(item,index) in all'>
				<div :id='index' class="one__note">
					<button type="button" class="close float-right" aria-label="Close" @click='deleteNote(item.id)'>
					  <span aria-hidden="true">&times;</span>
					</button>
					<h2>{{item.Title}}</h2>
					<p v-for='(next,index) in ArrowFilt(item.description.split("*"))' v-show='index <2' class='nextText'>{{next | trim}}</p>

					<!-- <p v-for='(point,index) in all2' v-if='item.id == index+1'>{{point}}</p> -->			
					<button @click='change(item.id)'>Редактировать</button>
					<!-- ниже условие. Кладем в функцию элемент массива объектов, его сплит по условию-разделителю. Которая вернет нам отфильтрованное значение  -->
					<img v-if='ArrowFilt(item.description.split("*")).length>2' src="~/assets/down-arrow.png" alt="раскрыть" style="max-width:8%;float:right;" @click="showArrow" class='showArrow'>
				</div>
			</div>
    	</div>
    	<div v-if='add'>    		
    			<input type="text" class="form-control mt-3" placeholder="Название" v-model='title'>
	    		<div id="place">
	    			<input type="text" class="form-control mt-2" v-show='title!=""' placeholder="Добавить пункт" v-model='points'>    						
	    		</div>    		
    		<button class="btn btn-success mt-2" v-show='title!=""' @click='more'>Еще</button>
    	</div>
    </div>
    <div class="container mt-3" v-if='add'>
    	<h3>{{title}}</h3>
    	<ul>
    		<div v-for='(li,index) in copy'>
    			<li>{{li}}</li>
    			<button type="button" class="close" aria-label="Close" @click='close(index)'>
				  <span aria-hidden="true">&times;</span>
				</button>
    		</div>
    	</ul>
    	<button class="btn btn-dark mt-2" v-show='copy.length > 0' @click='saveNew'>Сохранить</button>
    </div>
  </div>
</template>

<script>

import JQuery from 'jquery'
 	let $ = JQuery

export default {
	async asyncData({ $axios,query}){
		//let all = await $axios.$get('https://maximum-movies.com/todo')
		const token = query.token;

		var formData = require('form-data');
		var obj = new formData();
		obj.append('token', token)		

		let all = await fetch('https://maximum-movies.com/todo',{
			method: 'post',
		    body:obj
		}).then((res)=>{
			return res.json();
		})
		.then((data)=>{
			return data;
		})
		//let token = query.user
		return { all }
	},
	data(){
		return{
			add:false,
			title:'',
			points:'',
			copy:[],
			count:'',
		}
	},
	filters: { // ФИЛЬТРЫ
	    capitalize: function(item) {
	        if (item != " "){
	        	return item
	        }
	    },
	    trim: function(item){ //вырезание пробелов
	    	return item.trim()
	    }
	},
	mounted(){
		this.setCookie()
	},
	components: {
        	
        },
	methods:{
		setCookie(){
		    document.cookie = "user" + "=" + this.$route.query.user;
		  },
		// async Bell(){ ИСПОЛЬЗОВАТЬ ДЛЯ ДИНАМИЧЕСКОЙ ПОДГРУЗКИ КОНТЕНТА ПО КЛИКУ
		// 	const page = 1
		// 	var FormData = require('form-data'); //собираем данные для отправки через FormData
		// 	    var objBell = new FormData();
		// 	    objBell.append('page', page);

		// 	    let AwesomeBell = await fetch('https://maximum-movies.com/todo',{ //делаем POST запрос
		//           method: 'post',
		//           body:objBell
		// 	       }).then((res) => {
		//             return res.json();
		//         })
		//         .then((data) => {
		//         	return data
		//         })
		//         this.all = AwesomeBell // для обновления наших данных в динамическом рендеринге страницы (получается без перезагрузки)
		// },
			async deleteNote(data){ // удаление целой заметки по id
				var id_note = data

				var FormData = require('form-data');
				var obj = new FormData();
				obj.append('idDel', id_note)

				let req = await fetch('https://maximum-movies.com/todochange',{
					method: 'post',
		          	body:obj
				}).then((res)=>{
					return res.json();
				})
				.then((data)=>{
					return data;
				})

				this.all = req
			},
			change(id){ //переходим к редактированию заметки
				this.$router.push({
					path:'/notes/'+ id,
					query:{token:this.$route.query.token}
				})
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
			async saveNew(){ // добаляем новую заметку в БД			
				const token = localStorage.getItem('Utoken')
				const title = this.title
				const description = this.copy.join("*") //в одну строку массив с разделителем *

				var FormData = require('form-data'); //собираем данные для отправки через FormData
			    var obj = new FormData();
			    obj.append('title', title);
			    obj.append('description', description);
			    obj.append('token',token)

			    //кладем в переменную, что бы динамически обновить asyncData
			    let Awesome = await fetch('https://maximum-movies.com/todochange',{ //делаем POST запрос
		          method: 'post',
		          body:obj
			       }).then((res) => {
		            return res.json();
		        })
		        .then((data) => {
		        	return data
		        })
		        this.all = Awesome // для обновления наших данных в динамическом рендеринге страницы (получается без перезагрузки)

		        //ниже очищаем инпуты и контейнер в который пушились элементы
		        this.add = false
		        this.copy = []
		        this.title = ''
			},
			ArrowFilt(data){ //функция фильтр заметок. В data кладется элемент из массива
				let some = data.filter(this.$options.filters.capitalize) //обращаемся к локальному фильтру 
				return some // возвращаем значение после фильтра
				//console.log(some)
			},
			showArrow(event){ // раскрываем заметки которых больше 2-х
				
				let taran = event.target.parentNode //получаем родителя элемента по которому кликнули

				var arr = $('.one__note').find('.nextText') //находим все пункты (теги), даже те которые d-none

				for(var ass=0; ass<arr.length;ass++){
					//если скрытые элементы принадлежат одному родителю что и событие, то отобразим в родителе скрытые элементы
					if(arr[ass].style.display == 'none' && arr[ass].parentNode == taran ){
						$(arr[ass]).slideDown()
						arr[ass].style.display = 'block'											
					}
					//если начальные элементы не скрыты, то их и не скрываем а скрываем те, которые были открыты специально
					else if(arr[ass].style.display == 'block' && arr[ass].parentNode == taran){
						$(arr[ass]).slideUp()
						//arr[ass].style.display = 'none'
					}				
				}
				// АНИМАЦИЯ стрелочки по которой кликнули
				var name_class = event.target.className //получаем название класса по которому кликнули
				var el = event.target //получаем элемент по которому кликнули
				if(name_class == "showArrow"){
					el.classList.add('rotate');//добавляем Класс элементу
					taran.classList.add('height_anim')//добавляем Класс его родителю
				}else{
					el.classList.remove('rotate');//удаляем Класс у элемента
					taran.classList.remove('height_anim')//удаляем Класс у родителя
				}				
			},
	}
}
</script>

<style scoped>
	button{
		outline: 0;
	}
	#all__notes{
		display:grid;
		grid-template-columns:repeat(3,1fr);
		justify-items: center;
		gap:2rem;
		
	}
	.one__note{
		padding:15px;
		width:250px;
	    max-height: 200px;
	    transition: all 1.5s ease-in-out;
	    overflow: hidden;
		border:1px solid #0f0f0f;
		position:relative;	
	}
	.height_anim{
		max-height: 1000px;
		transition: all 1s ease-in-out;
	}
	.showArrow{
		cursor:pointer;
		transition:all 0.4s;
		position:absolute;
		bottom:15%;
		right:10%;
	}
	.rotate{
		transform:rotate(180deg);
	}
	#added_btn{
		border-radius: 1px 1px 10px 10px;
	}
	@media (min-width:250px) and (max-width:700px){
		#all__notes{
			display:block;			
		}
		.one__note{
			margin:0 auto;
		}
	}
</style>
