<template>
	<div id='area'>
		<div id="auth_check" v-if='all'>
			<div class="text-left">
				<h2>Авторизуйтесь что бы продолжить</h2>				
			</div>

			<form id="auth" v-if='reg == false' class="animated fadeIn">
				<input type="email" placeholder="email" class="form-control mt-3" v-model='email'>
				<input type="password" placeholder="пароль" class="form-control mt-3" v-model='pass'>
				<button class="btn btn-info mt-3 w-50" @click.prevent='auth'>Вход</button>
				<button class="btn btn-light mt-3 w-50" @click.prevent='reg=true'>регистрация</button>			
			</form>			

			<form id="reg" v-if='reg' class="animated fadeIn">
				<input type="text" class="form-control mt-3" placeholder="Имя" v-model='name'>
				<input type="email" class="form-control mt-3" placeholder="email" v-model='email'>
				<input type="password" class="form-control mt-3" placeholder="пароль" v-model='pass'>
				<button class="btn btn-info mt-3 w-50" @click.prevent='regi()'>Зарегистрироваться</button>
			</form>
		</div>
		<div class="container" v-if='all == false'>
			<h2>Проверьте свою почту</h2>
		</div>
	</div>
</template>
<script>
import JQuery from 'jquery'
 	let $ = JQuery

	export default{
		data(){
			return{
				all:true,
				reg:false,
				name:'',
				email:'',
				pass:''
			}
		},
		methods:{
			async auth(){
				//this.$router.push('/notes')
				var formData = require('form-data'); //собрали данные для отправки через FormData
			    var obj = new formData();
			    obj.append('email',this.email);
		    	obj.append('pass',this.pass);

			    let req = await fetch('https://maximum-movies.com/authorization',{
		    		method: 'post',
		          	body:obj
			    	}).then((res)=>{
			    		return res.json();
			    	})
			    	.then((data)=>{
			    		return data;
			    	})
			    
			    $('#area').fadeOut(()=>{
			    	this.$router.push({
						query:{token:req.token},
						path:'/notes'
					})
			    })
			    
				
			},
			regi(){
				var arr = []
				arr.push(this.name,this.email,this.pass)
				
				var login = this.name
				var email = this.email
				var pass = this.pass

				var formData = require('form-data'); //собрали данные для отправки через FormData
			    var obj = new formData();

		    	obj.append('login',login);
		    	obj.append('email',email);
		    	obj.append('pass',pass);

		    	let req = fetch('https://maximum-movies.com/auth-check',{
		    		method: 'post',
		          	body:obj
			    	}).then((res)=>{
			    		return res.json();
			    	})
			    	.then((data)=>{
			    		return data;
			    	})
			    console.log(req)
			    this.all = false
			}
		}
	}
</script>
<style>
	#area{
		position:absolute;
		width:100vw;
		height:100vh;
		backdrop-filter: blur(55px);
		z-index:999;
	}
	#auth_check{
		position: absolute;
		width:500px;
	    top: 40%;
	    left: 50%;
	    transform: translate(-50%,-50%);
	}
</style>