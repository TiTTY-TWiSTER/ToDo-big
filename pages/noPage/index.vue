<template>
	<div>
		<div class="text-center">
			<h2>This is no PAGE</h2>
			<ul>
				<li v-for='item in datas'><img :src="item.thumbnailUrl" alt=""></li>
			</ul>
		</div>
	</div>
</template>
<script>
	export default{
		data(){
			return{
				datas:[]
			}
		},
		async asyncData(){
			let data = await fetch('https://jsonplaceholder.typicode.com/photos').then(res=>res.json());
			return{
				datas:data
			}
		}
	}
</script>