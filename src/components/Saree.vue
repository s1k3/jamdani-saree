<template>
  <div class="w-100 h-100" style="position: relative;top:0;" @mouseover="hovered = true" @mouseleave="hovered = false">
    <img :src="saree.link"  style="z-index:1"/>
    <div class="hover w-100 h-100" v-if="hovered"></div>
    <button class="thm-btn md-btn fill-btn hover-button" v-if="hovered" @click="$emit('click')">নকশা তৈরি করুণ</button> 
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
  mounted(){

  },
  computed: {

  },
  methods:{
    
  }
}
</script>
<style scoped>
.hover-button{
	position: absolute;
	top: 50px;
	left: 85px;
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
</style>