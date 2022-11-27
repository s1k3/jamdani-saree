<template>
	<div class="w-100 h-100" style="position: relative;top:0;" @mouseover="hovered = true" @mouseleave="hovered = false">
		<img  :src="saree.link"  style="z-index:1" @click="$emit('click')"/>
		<div class="hover w-100 h-100" v-if="hovered"></div>
		<button class="thm-btn sml-btn fill-btn hover-button" v-if="hovered" @click="$emit('click')">নকশা তৈরি করুণ</button> 
	</div>
</template>
<script>
	export default {
		name: 'SareeComponent',
		props:{ saree:Object },
		data(){
			return{
				hovered:false
			}
		},
		mounted(){},
		computed: {

		},
		methods:{

		}
	}
</script>
<style scoped>

	body {
		font-family: 'kalpurushregular';  

	}
	.hover-button{
		position: absolute;
		top: 40%;
		left: 28%;
		margin: 0 auto;
		z-index:3;
	}
	.hover{
		background:black;
		position: absolute;
		top:0;
		left:0;
		z-index:2;
		opacity:0.5;


	}

	img.full_img:hover  {
		background: #25a94a;
		color: #85723d;
		font-size: 150%;
		-webkit-transform: scale(1.4);
		-ms-transform: scale(1.4);
		transform: scale(1.4);
		transition: 1s ease;
		border-radius: 5%;
	}

	@media only screen and (max-width: 768px) {
		.hover-button{
			position: absolute;
			top: 30px;
			left: 43px;
			z-index: 3;
		}
	}
</style>