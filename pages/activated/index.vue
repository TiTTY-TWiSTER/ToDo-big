<template>
	<div class="container mt-5">
		<h1>Спасибо за регистрацию!</h1>
		<div class="mt-3" style="color:gray;">
			<h4>Сейчас вы будите перенаправленны</h4>
		</div>
		<!-- <h1>{{$route.query.token}}</h1> -->
	</div>
</template>
<script>
	export default{
		async asyncData({query}){
			//const some = Object.keys(query)[0].split('token') //получаем только ключ из url (это когда только ?kakieto_dannie)
			const token = query.token

			var formData = require('form-data'); //собрали данные для отправки через FormData
			var obj = new formData();			
			obj.append('token',token);

			let req = await fetch('https://maximum-movies.com/activated',{
				method: 'post',
		        body:obj
			}).then((res)=>{
				return res.json();
			})
			.then((data)=>{
				return data;
			})
			return{req}
		},
		mounted(){
			console.log(this.req.id)
			console.log(this.$route)
			//console.log(this.$route.query.token)
	    	//console.log(this.some.filter(this.$options.filters.capitalize))
	    	if(this.req.activated == 1){
	    		setTimeout(()=>{
			    	this.$router.push({
			    		query:{token:this.$route.query.token},
			    		path:'/notes'
			    	})		    	
		    	},3000)
	    	}	    	
	    },
	    filters:{
	    	capitalize:function(item){
	    		if(item != ""){
	    			return item;
	    		}
	    	}
	    },
	}
</script>
<style>
#area{
	display:none;
}
</style>