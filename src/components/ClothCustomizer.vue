<template>
  <div>
   <loading :active.sync="isBuildingSaree" :can-cancel="false" :is-full-page="true"/>
   <div class="row m-5" v-if="!hasSelectedSaree">
    <div class="col-lg-12">
     <!-- <input class="form-control mb-2" placeholder="আপনার পছন্দের শাড়ি খুঁজুন; লাল পার | লাল কাজ | ফুল বডি কাজ | হালকা কাজ" v-model="search"/> -->

<div id="search-wrapper" >
  <i class="search-icon fas fa-search"></i>
<input type="text" id="search" placeholder="আপনার পছন্দের শাড়ি খুঁজুন; লাল পার | লাল কাজ | ফুল বডি কাজ | হালকা কাজ" v-model="search"/>
<!-- <button id="search-button">Search</button> -->
</div>

     <div class="row" style="padding-top: 30px;">
      <div class="col-lg-4 mb-2 p-2" 
      v-for="(saree,index) in filteredSarees" 
      :key="index">
      <saree :saree="saree"  @click="startCustomization(saree.index)"/>
    </div>
    <h4 v-if="search !== '' && filteredSarees.length === 0" class="text-center" style="color:red">কোন শাড়ি খুঁজে পাওয়া যায়নি</h4>
  </div>
  <div class="w-100" v-if="search === ''">
    <button class="thm-btn mt-30 sml-btn lft-icon brd-btn mr-5" 
    href="javascript:void(0);"
    @click="loadMoreSarees" 
    v-if="load_more_sarees.length > 0"> আরো শাড়ি দেখুন<span></span>
  </button>
</div>
</div>
</div>
<div class="row m-0 p-0" v-if="hasSelectedSaree">
 <div class="col-lg-12">
  <div id="menu-image" class="d-flex justify-content-center">
    <a href="javascript:void(0);" @click="step = 1" >
      <img src="button/button-suta.png">
      <p :class="{'active-step':step === 1}">সুতার কাউন্ট</p>
    </a>
    <a href="javascript:void(0);" @click="step = 2">
      <img src="button/button-color.png">
      <p :class="{'active-step':step === 2}">কাপড়ের রং</p>
    </a>
    <a href="javascript:void(0);" @click="step = 3">
      <img src="button/button-pair.png">
      <p :class="{'active-step':step === 3}">পাড়ের নকশা</p>
    </a>
    <a href="javascript:void(0);" @click="step = 4">
      <img src="button/button-achol.png">
      <p :class="{'active-step':step === 4}">আঁচলের নকশা</p>
    </a>
    <a href="javascript:void(0);" @click="step = 5">
      <img src="button/button-body.png">
      <p :class="{'active-step':step === 5}">জমিনের নকশা</p>
    </a>
  </div>
</div>
<div class="col-lg-6">

 <div id="capture">
  <img v-if="saree_image !== null" :src="saree_image" style="transform:scale(0.90)"/>
</div>
<div class="w-100" style="padding-bottom: 50px;">
  <button class="thm-btn mt-30 sml-btn lft-icon brd-btn mr-5 btn-w-100" 
  @click="resetEveryThing">
  শাড়ি পছন্দ করুণ <span></span>
</button>            
<button class="thm-btn mt-30 sml-btn lft-icon brd-btn mr-5 btn-w-100" 
@click="downloadImage">
ডাউনলোড<span></span>
</button>
<button class="thm-btn mt-30 sml-btn lft-icon brd-btn mr-5 btn-w-100 mb-2" 
v-if="totalPrice !== 0"
data-toggle="modal"
data-target="#priceModal">
মূল্যঃ {{numberWithCommas(totalPrice)}} টাকা<span></span>
</button>
</div>
</div>
<div class="col-lg-6 m-0 p-0">
  <div class="row m-0 p-0">
    <div class="col-lg-12 mb-70" v-if="step == 1">
     <h3>সুতার কাউন্ট নির্বাচন করুন</h3>
     <div class="row"> 
      <div class="col-md-6 col-lg-6"  v-for="(yarn_type,index) in yarn_types" :key="index"> 
        <div class="form-check float-left">
         <input :id="`count${index}`" 
         type="radio" 
         v-model="yarn_count"
         :value="yarn_type" 
         class="mr-1"/>
         <label :for="`count${index}`" 
         :class="{'selected-count-color': yarn_type.title === yarn_count.title}"
         class="form-check-label">
         {{ yarn_type.title }}
       </label>
     </div>
   </div>
 </div>
 <p class="clearfix"></p>
</div>
<div class="col-lg-12" v-if="step == 2">
 <div class="row">
  <div  class="col-lg-12" style="overflow-y: scroll;height:400px">
   <h3>রঙ নির্বাচন করুন</h3>
   <img :src="background.image" 
   v-for="(background,index) in backgrounds"  
   :key="index" 
   class="p-2"
   @click="selectBackground(index)"
   style="width:100px;height:100px;"
   :class="[index ===  selectedBackground? 'border-red' : 'border-transparent','background-image']"/> 
 </div>
</div>
</div>
<div class="col-lg-12" v-if="step == 3">
 <div class="row">
  <div  class="col-lg-12" style="overflow-y: scroll;height:400px">
   <h3>শাড়ির পাড় নির্বাচন করুন</h3>
   <img :src="pair.thumbnail" 
   v-for="(pair,index) in pairs"  
   :key="index" 
   class="p-2"
   @click="selectPair(index)"
   :class="[index ===  selectedPair? 'border-red' : 'border-transparent','background-image']"/>
 </div>
</div>
</div>
<div class="col-lg-12" v-if="step == 4">
 <div class="row">
  <div  class="col-lg-12" style="overflow-y: scroll;height:400px">
   <h3>শাড়ির আঁচল নির্বাচন করুন</h3>
   <img :src="achol.thumbnail" 
   v-for="(achol,index) in achols"  
   :key="index" 
   class="p-2"
   @click="selectAchol(index)"
   style="width:100px;height:100px;"
   :class="[index ===  selectedAchol? 'border-red' : 'border-transparent','background-image']"/>
 </div>
</div>
</div>
<div class="col-lg-12" v-if="step == 5">
 <div class="row">
  <div  class="col-lg-12" style="overflow-y: scroll;height:400px">
   <h3>শাড়ির জমিন নির্বাচন করুন</h3>
   <img :src="grid.thumbnail" 
   v-for="(grid,index) in grids"  
   :key="index" 
   class="p-2"
   @click="selectGrid(index)"
   style="width:100px;height:100px;"
   :class="[index ===  selectedGrid? 'border-red' : 'border-transparent','background-image']"/> 
 </div>
</div>
</div>
</div>
</div>
</div>
<div class="modal" tabindex="-1" role="dialog" id="priceModal" style="top:30px;">
  <div class="modal-dialog" role="document">
   <div class="modal-content">
    <div class="modal-header">
     <h5 class="modal-title" style="text-align: center;">শাড়ির মূল্যঃ {{numberWithCommas(totalPrice)}} টাকা</h5>
     <button type="button" class="close" data-dismiss="modal" aria-label="Close">
       <span aria-hidden="true">&times;</span>
     </button>
   </div>
   <div class="modal-body" style=" background-image: url(/assets/images/modal.png); background-repeat: no-repeat;
   background-position: 0 0;
   background-size: cover">
   <table class="table" >
    <thead>
     <tr style="background-color:rgb(223, 238, 214); ">
      <th scope="col">বিবরণ</th>
      <th scope="col" style="text-align: center;">মূল্য</th>
    </tr>
  </thead>
  <tbody>
    <tr>
    <th scope="row">সুতার কাউন্ট</th>
    <td><b> {{yarn_count.title}}</b></td>
  </tr>
   <tr>
    <th scope="row">সুতার কাউন্টের মূল্য</th>
    <td><b> {{numberWithCommas(yarn_count.price/4)}} টাকা</b></td>
  </tr>
  <tr>
    <th scope="row">কাপড়ের রং</th>
    <td><b> {{background.name}}</b></td>
  </tr>
  <tr>
    <th scope="row">কাপড়ের রং এর মূল্য</th>
    <td><b> {{numberWithCommas(background.price)}} টাকা</b></td>
  </tr>
  <tr>
    <th scope="row">আঁচল</th>
    <td><b> {{achol.name}}</b></td>
  </tr>
  <tr>
    <th scope="row">আঁচলের  মূল্য</th>
    <td><b> {{numberWithCommas(achol.price)}} টাকা</b></td>
  </tr>
  <tr>
    <th scope="row">পাড়</th>
    <td><b> {{pair.name}}</b></td>
  </tr>
  <tr>
    <th scope="row">পাড়ের মূল্য</th>
    <td><b> {{numberWithCommas(pair.price)}} টাকা</b></td>
  </tr>
  <tr>
    <th scope="row">জমিন</th>
    <td><b> {{grid.name}}</b></td>
  </tr>
  <tr>
    <th scope="row">জমিনের মূল্য</th>
    <td><b> {{numberWithCommas(grid.price)}} টাকা</b></td>
  </tr>
  <tr>
    <th scope="row">শ্রমিকের (কার্যদিবস) মজুরি </th>
    <td><b> {{numberWithCommas(yarn_count.price-(yarn_count.price/4))}} টাকা</b></td>
  </tr>
  <tr style="background-color:rgb(223, 238, 214);">
    <th scope="row">মোট</th>
    <td><b> {{numberWithCommas(totalPrice)}} টাকা</b></td>
  </tr>
</tbody>
</table>
</div>
</div>
</div>
</div>
</div>

</template>

<script>
  import html2canvas from 'html2canvas';
  import mergeImages from 'merge-images';
  import Loading from 'vue-loading-overlay';
  import 'vue-loading-overlay/dist/vue-loading.css';
  import Saree from './Saree';
  export default {
    name: 'ClothCustomizer',
    components:{
      Saree,
      Loading
    },
    data(){
      return{
        background: {tag:"",image:"", price: 0},
        pair: {image:"", thumbnail:"", price: 0},
        achol: {image:"", thumbnail:"", price: 0},
        grid: {image:"", thumbnail:"", price: 0},
        isBuildingSaree:false,
        saree_image:null,
        hasSelectedSaree:false,
        selectedSaree:-1,
        step:1,
        selectedBackground:-1,
        selectedPair:-1,
        selectedAchol:-1,
        selectedGrid:-1,
        yarn_count: {title:"২০/২৬ কাউন্ট সুতা", price:"4000"},
        search:"",
        yarn_types:[
        {title:"২০/২৬ কাউন্ট সুতা", price:"4000"},
        {title:"৩২ কাউন্ট সুতা", price:"4500"},
        {title:"৪০ কাউন্ট সুতা", price:"8000"},
        {title:"৪০+৮০  কাউন্ট সুতা", price:"3000"},
        {title:"৬০+৮০ কাউন্ট সুতা", price:"5000"},
        {title:"৮০ কাউন্ট সুতা", price:"14000"},
        {title:"৮৪ কাউন্ট সুতা", price:"25000"},
        {title:"১০০ কাউন্ট সুতা", price:"50000"},
        {title:"১২০ কাউন্ট সুতা", price:"100000"},
        {title:"১৫০ কাউন্ট সুতা", price:"120000"},
        {title:"২০০ কাউন্ট সুতা", price:"150000"},
        ],
        sarees:[

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-14.png",background:23,pair:32,achol:53,grid:52,index:0},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-1.png",background:6,pair:2,achol:6,grid:14,index:1},

        {name:"Name",tag:["হালকা কাজ", "নীল পার", "নীল কাজ","halka kaj","nil kaj", "nil par","blue", "asmani"], link:"sarees/28.jpg",background:50,pair:49,achol:49,grid:46,index:2},

        {name:"Name",tag:["হালকা কাজ","বাসন্তি","কমলা","সুরমা কাজ","orange","surma","bashonti","halka kaj", "pair", "holud", "yellow"], link:"sarees/22.jpg",background:52,pair:47,achol:47,grid:31,index:3},

        {name:"Name",tag:["লাল কাজ", "নীল আচল","হালকা কাজ","blue achol","red kaj", "red", "nil", "lal" ], link:"sarees/s-12.png",background:27,pair:40,achol:18,grid:22,index:4},

        {name:"Name",tag:["লাল পার", "ফুল বডি কাজ", "full body kaj", "lal", "red", "megenda", "মেজেন্ডা পার" ], link:"sarees/s-6.png",background:5,pair:27,achol:12,grid:10,index:5},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-2.png",background:8,pair:2,achol:14,grid:6,index:6},

        
        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-16.png",background:24,pair:3,achol:3,grid:3,index:7},

        {name:"Name",tag:["ফুল বডি কাজ","গোলাপি পার", "গোলাপি কাজ", "বাসন্তি", "কমলা","full body kaj","golapi kaj","orange"], link:"sarees/s-11.png",background:1,pair:5,achol:21,grid:14,index:8},



        {name:"Name",tag:["ফুল বডি কাজ","গোলাপি পার", "গোলাপি কাজ", "বাসন্তি", "কমলা","full body kaj","golapi kaj","orange"], link:"sarees/s-9.png",background:16,pair:20,achol:6,grid:16,index:9},


        {name:"Name",tag:["ফুল বডি কাজ","গোলাপি পার", "গোলাপি কাজ", "বাসন্তি", "কমলা","full body kaj","golapi kaj","orange"], link:"sarees/s-10.png",background:6,pair:21,achol:17,grid:14,index:10},

        {name:"Name",tag:["হালকা কাজ","সুরমা কাজ","ash color","ash","surma", "halka kaj"], link:"sarees/24.jpg",background:10,pair:42,achol:19,grid:31,index:11},

        {name:"Name",tag:["হালকা কাজ","সুরমা কাজ","ash color","ash","surma", "halka kaj"], link:"sarees/s-13.png",background:14,pair:13,achol:13,grid:3,index:12},


        {name:"Name",tag:["ফুল বডি কাজ","গোলাপি পার", "গোলাপি কাজ", "বাসন্তি", "কমলা","full body kaj","golapi kaj","orange"], link:"sarees/s-7.png",background:21,pair:27,achol:12,grid:10,index:13},


        {name:"Name",tag:["ফুল বডি কাজ","গোলাপি পার", "গোলাপি কাজ", "বাসন্তি", "কমলা","full body kaj","golapi kaj","orange"], link:"sarees/s-4.png",background:14,pair:4,achol:4,grid:3,index:14},

        {name:"Name",tag:["ফুল বডি কাজ","গোলাপি পার", "গোলাপি কাজ", "বাসন্তি", "কমলা","full body kaj","golapi kaj","orange"], link:"sarees/s-5.png",background:4,pair:26,achol:15,grid:9,index:15},


        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-15.png",background:25,pair:24,achol:54,grid:54,index:16},


        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-16.png",background:24,pair:3,achol:3,grid:3,index:17},


        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-17.png",background:1,pair:2,achol:2,grid:5,index:18},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-18.png",background:2,pair:5,achol:16,grid:4,index:19},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-19.png",background:12,pair:32,achol:54,grid:55,index:20},

        








        



        ],
        load_more_sarees:[

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-20.png",background:7,pair:16,achol:9,grid:24,index:21},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-21.png",background:27,pair:12,achol:6,grid:5,index:22},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-22.png",background:12,pair:13,achol:18,grid:54,index:23},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-23.png",background:16,pair:15,achol:11,grid:24,index:24},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-24.png",background:19,pair:23,achol:18,grid:21,index:25},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-25.png",background:20,pair:22,achol:19,grid:6,index:26},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-26.png",background:25,pair:11,achol:3,grid:4,index:27},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-27.png",background:17,pair:4,achol:55,grid:24,index:28},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-28.png",background:21,pair:6,achol:7,grid:16,index:29},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-29.png",background:0,pair:0,achol:21,grid:19,index:30},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-30.png",background:14,pair:15,achol:12,grid:18,index:31},

        {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-31.png",background:26,pair:0,achol:11,grid:22,index:32},

         {name:"Name",tag:["গোলাপি পার", "গোলাপি কাজ","golapi","komola" ,"হালকা কাজ", "সাদা"], link:"sarees/s-32.png",background:4,pair:2,achol:6,grid:14,index:33},

        ],
        backgrounds:[
        
        {name:"Name",image:"backgrounds/bg-43.png",price: "2000"},
        {name:"Name",image:"backgrounds/bg-44.png",price: "2200"},
        {name:"Name",image:"backgrounds/bg-45.png",price: "2700"},
        {name:"Name",image:"backgrounds/bg-46.png",price: "2900"},
        {name:"Name",image:"backgrounds/bg-47.png",price: "2000"},
        {name:"Name",image:"backgrounds/bg-48.png",price: "2700"},
        {name:"Name",image:"backgrounds/bg-49.png",price: "2700"},
        {name:"Name",image:"backgrounds/bg-50.png",price: "2600"},
        {name:"Name",image:"backgrounds/bg-51.png",price: "3600"},
        {name:"Name",image:"backgrounds/bg-52.png",price: "2300"},
        {name:"Name",image:"backgrounds/bg-53.png",price: "2500"},
        {name:"Name",image:"backgrounds/bg-54.png",price: "3600"},
        {name:"Name",image:"backgrounds/bg-55.png",price: "2300"},
        {name:"Name",image:"backgrounds/bg-56.png",price: "2500"},
        {name:"Name",image:"backgrounds/bg-57.png",price: "3600"},
        {name:"Name",image:"backgrounds/bg-58.png",price: "3300"},
        {name:"Name",image:"backgrounds/bg-59.png",price: "2500"},
        {name:"Name",image:"backgrounds/bg-60.png",price: "2500"},
        {name:"Name",image:"backgrounds/bg-61.png",price: "3600"},
        {name:"Name",image:"backgrounds/bg-62.png",price: "3300"},
        {name:"Name",image:"backgrounds/bg-63.png",price: "2500"},
        {name:"Name",image:"backgrounds/bg-64.png",price: "2240"},
        {name:"Name",image:"backgrounds/bg-65.png",price: "2700"},
        {name:"Name",image:"backgrounds/bg-66.png",price: "1500"},
        {name:"Name",image:"backgrounds/bg-67.png",price: "1890"},
        {name:"Name",image:"backgrounds/bg-68.png",price: "1590"},
        {name:"Name",image:"backgrounds/bg-69.png",price: "1870"},
        {name:"Name",image:"backgrounds/bg-70.png",price: "2100"},


        {name:"Name",image:"backgrounds/body850-1.png",price: "2000"},
        {name:"Name",image:"backgrounds/body850-2.png",price: "2150"},
        {name:"Name",image:"backgrounds/body850-3.png",price: "2300"},
        {name:"Name",image:"backgrounds/body850-4.png",price: "4000"},
        {name:"Name",image:"backgrounds/body850-5.png",price: "3200"},
        {name:"Name",image:"backgrounds/body850-6.png",price: "1800"},
        {name:"Name",image:"backgrounds/body850-7.png",price: "1740"},
        {name:"Name",image:"backgrounds/body850-8.png",price: "5000"},
        {name:"Name",image:"backgrounds/body850-9.png",price: "2490"},
        {name:"Name",image:"backgrounds/body850-11.png",price: "2300"},
        {name:"Name",image:"backgrounds/body850-12.png",price: "5200"},
        {name:"Name",image:"backgrounds/body850-13.png",price: "2900"},
        {name:"Name",image:"backgrounds/body850-14.png",price: "3800"},
        {name:"Name",image:"backgrounds/body850-15.png",price: "9000"},
        {name:"Name",image:"backgrounds/body850-16.png",price: "1200"},
        {name:"Name",image:"backgrounds/body850-17.png",price: "2990"},
        {name:"Name",image:"backgrounds/body850-18.png",price: "2300"},
        {name:"Name",image:"backgrounds/body850-19.png",price: "3100"},
        {name:"Name",image:"backgrounds/body850-20.png",price: "3240"},
        {name:"Name",image:"backgrounds/body850-21.png",price: "2400"},
        {name:"Name",image:"backgrounds/body850-22.png",price: "2550"},
        {name:"Name",image:"backgrounds/body850-23.png",price: "2700"},
        {name:"Name",image:"backgrounds/body850-24.png",price: "2800"},
        {name:"Name",image:"backgrounds/body850-25.png",price: "3450"},
        {name:"Name",image:"backgrounds/body850-26.png",price: "2500"},
        {name:"Name",image:"backgrounds/body850-27.png",price: "1200"},
        {name:"Name",image:"backgrounds/body850-28.png",price: "2100"},
        {name:"Name",image:"backgrounds/body850-29.png",price: "1900"},
        {name:"Name",image:"backgrounds/body850-30.png",price: "2000"},
        {name:"Name",image:"backgrounds/body850-31.png",price: "1400"},
        {name:"Name",image:"backgrounds/body850-32.png",price: "1300"},
        {name:"Name",image:"backgrounds/body850-33.png",price: "1200"},
        {name:"Name",image:"backgrounds/body850-34.png",price: "1100"},
        {name:"Name",image:"backgrounds/body850-35.png",price: "850"},
        {name:"Name",image:"backgrounds/body850-36.png",price: "6000"},
        {name:"Name",image:"backgrounds/body850-37.png",price: "2000"},
        {name:"Name",image:"backgrounds/body850-38.png",price: "3000"},
        {name:"Name",image:"backgrounds/body850-39.png",price: "2100"},
        {name:"Name",image:"backgrounds/body850-40.png",price: "2200"},
        {name:"Name",image:"backgrounds/body850-41.png",price: "2300"},
        {name:"Name",image:"backgrounds/body850-42.png",price: "1800"},
        ], 
        pairs:[
        {name:"Name",image:"pairs/Down Boarder 01.png",image_rotated:"pairs/Upper Boarder 01.png",thumbnail:"pairs/pair-bg-1.png",price:"700"},
        {name:"Name",image:"pairs/Down Boarder 02.png",image_rotated:"pairs/Upper Boarder 02.png",thumbnail:"pairs/pair-bg-2.png",price:"800"},
        {name:"Name",image:"pairs/Down Boarder 03.png",image_rotated:"pairs/Upper Boarder 03.png",thumbnail:"pairs/pair-bg-3.png",price:"1100"},
        {name:"Name",image:"pairs/Down Boarder 04.png",image_rotated:"pairs/Upper Boarder 04.png",thumbnail:"pairs/pair-bg-4.png",price:"700"},
        {name:"Name",image:"pairs/Down Boarder 05.png",image_rotated:"pairs/Down Boarder 05.png",thumbnail:"pairs/pair-bg-5.png",price:"800"},
        {name:"Name",image:"pairs/Down Boarder 06.png",image_rotated:"pairs/Down Boarder 06.png",thumbnail:"pairs/pair-bg-6.png",price:"500"},
        {name:"Name",image:"pairs/Down Boarder 07.png",image_rotated:"pairs/Down Boarder 07.png",thumbnail:"pairs/pair-bg-7.png",price:"800"},
        {name:"Name",image:"pairs/Down Boarder 08.png",image_rotated:"pairs/Down Boarder 08.png",thumbnail:"pairs/pair-bg-8.png",price:"800"},
        {name:"Name",image:"pairs/Down Boarder 09.png",image_rotated:"pairs/Down Boarder 09.png",thumbnail:"pairs/pair-bg-9.png",price:"700"},
        {name:"Name",image:"pairs/Down Boarder 10.png",image_rotated:"pairs/Down Boarder 10.png",thumbnail:"pairs/pair-bg-10.png",price:"900"},
        {name:"Name",image:"pairs/Down Boarder 11.png",image_rotated:"pairs/Down Boarder 11.png",thumbnail:"pairs/pair-bg-11.png",price:"1600"},
        {name:"Name",image:"pairs/Down Boarder 12.png",image_rotated:"pairs/Down Boarder 12.png",thumbnail:"pairs/pair-bg-12.png",price:"950"},
        {name:"Name",image:"pairs/Down Boarder 13.png",image_rotated:"pairs/Down Boarder 13.png",thumbnail:"pairs/pair-bg-13.png",price:"750"},
        {name:"Name",image:"pairs/Down Boarder 14.png",image_rotated:"pairs/Down Boarder 14.png",thumbnail:"pairs/pair-bg-14.png",price:"1050"},
        {name:"Name",image:"pairs/Down Boarder 15.png",image_rotated:"pairs/Down Boarder 15.png",thumbnail:"pairs/pair-bg-15.png",price:"1900"},
        {name:"Name",image:"pairs/Down Boarder 16.png",image_rotated:"pairs/Down Boarder 16.png",thumbnail:"pairs/pair-bg-16.png",price:"800"},
        {name:"Name",image:"pairs/Down Boarder 17.png",image_rotated:"pairs/Down Boarder 17.png",thumbnail:"pairs/pair-bg-17.png",price:"490"},

        {name:"Name",image:"pairs/Down Boarder 18.png",image_rotated:"pairs/Down Boarder 18.png",thumbnail:"pairs/pair-bg-18.png",price:"1200"},
        {name:"Name",image:"pairs/Down Boarder 19.png",image_rotated:"pairs/Down Boarder 19.png",thumbnail:"pairs/pair-bg-19.png",price:"500"},
        {name:"Name",image:"pairs/Down Boarder 20.png",image_rotated:"pairs/Down Boarder 20.png",thumbnail:"pairs/pair-bg-20.png",price:"1600"},

        {name:"Name",image:"pairs/Down Boarder 21.png",image_rotated:"pairs/Down Boarder 21.png",thumbnail:"pairs/pair-bg-21.png",price:"800"},
        {name:"Name",image:"pairs/Down Boarder 22.png",image_rotated:"pairs/Down Boarder 22.png",thumbnail:"pairs/pair-bg-22.png",price:"900"},
        {name:"Name",image:"pairs/Down Boarder 23.png",image_rotated:"pairs/Down Boarder 23.png",thumbnail:"pairs/pair-bg-23.png",price:"800"},
        {name:"Name",image:"pairs/Down Boarder 24.png",image_rotated:"pairs/Down Boarder 24.png",thumbnail:"pairs/pair-bg-24.png",price:"1100"},

        {name:"Name",image:"pairs/Down Boarder 25.png",image_rotated:"pairs/Down Boarder 25.png",thumbnail:"pairs/pair-bg-25.png",price:"1200"},
        {name:"Name",image:"pairs/Down Boarder 26.png",image_rotated:"pairs/Down Boarder 26.png",thumbnail:"pairs/pair-bg-26.png",price:"1900"},
        {name:"Name",image:"pairs/Down Boarder 27.png",image_rotated:"pairs/Down Boarder 27.png",thumbnail:"pairs/pair-bg-27.png",price:"2000"},
        {name:"Name",image:"pairs/Down Boarder 28.png",image_rotated:"pairs/Down Boarder 28.png",thumbnail:"pairs/pair-bg-28.png",price:"1700"},

        {name:"Name",image:"pairs/Down Boarder 29.png",image_rotated:"pairs/Down Boarder 29.png",thumbnail:"pairs/pair-bg-29.png",price:"1750"},
        {name:"Name",image:"pairs/Down Boarder 30.png",image_rotated:"pairs/Down Boarder 30.png",thumbnail:"pairs/pair-bg-30.png",price:"1190"},
        {name:"Name",image:"pairs/Down Boarder 31.png",image_rotated:"pairs/Down Boarder 31.png",thumbnail:"pairs/pair-bg-31.png",price:"2200"},
        {name:"Name",image:"pairs/Down Boarder 32.png",image_rotated:"pairs/Down Boarder 32.png",thumbnail:"pairs/pair-bg-32.png",price:"1400"},
        {name:"Name",image:"pairs/Down Boarder 33.png",image_rotated:"pairs/Down Boarder 33.png",thumbnail:"pairs/pair-bg-33.png",price:"1150"},


        {name:"Name",image:"pairs/p-001.png",image_rotated:"pairs/p-001-rotated.png",thumbnail:"pairs/s-p-001.png",price:"600"},
        {name:"Name",image:"pairs/p-002.png",image_rotated:"pairs/p-002-rotated.png",thumbnail:"pairs/s-p-002.png",price:"450"},
        {name:"Name",image:"pairs/p-003.png",image_rotated:"pairs/p-003-rotated.png",thumbnail:"pairs/s-p-003.png",price:"700"},
        {name:"Name",image:"pairs/p-005.png",image_rotated:"pairs/p-005-rotated.png",thumbnail:"pairs/s-p-005.png",price:"1200"},
        {name:"Name",image:"pairs/p-006.png",image_rotated:"pairs/p-006-rotated.png",thumbnail:"pairs/s-p-006.png",price:"990"},
        {name:"Name",image:"pairs/p-007.png",image_rotated:"pairs/p-007-rotated.png",thumbnail:"pairs/s-p-007.png",price:"320"},
        {name:"Name",image:"pairs/p-008.png",image_rotated:"pairs/p-008-rotated.png",thumbnail:"pairs/s-p-008.png",price:"590"},
        {name:"Name",image:"pairs/p-009.png",image_rotated:"pairs/p-009-rotated.png",thumbnail:"pairs/s-p-009.png",price:"800"},
        {name:"Name",image:"pairs/p-0010.png",image_rotated:"pairs/p-0010-rotated.png",thumbnail:"pairs/s-p-0010.png",price:"900"},
        {name:"Name",image:"pairs/p-0011.png",image_rotated:"pairs/p-0011-rotated.png",thumbnail:"pairs/s-p-0011.png",price:"450"},
        {name:"Name",image:"pairs/p-0012.png",image_rotated:"pairs/p-0012-rotated.png",thumbnail:"pairs/s-p-0012.png",price:"620"},
        {name:"Name",image:"pairs/p-0013.png",image_rotated:"pairs/p-0013-rotated.png",thumbnail:"pairs/s-p-0013.png",price:"480"},
        {name:"Name",image:"pairs/p-0014.png",image_rotated:"pairs/p-0014-rotated.png",thumbnail:"pairs/s-p-0014.png",price:"510"},
        {name:"Name",image:"pairs/p-0015.png",image_rotated:"pairs/p-0015-rotated.png",thumbnail:"pairs/s-p-0015.png",price:"550"},
        {name:"Name",image:"pairs/p-16.png",image_rotated:"pairs/p-16-rotated.png",thumbnail:"pairs/s-p-16.png",price:"450"},
        {name:"Name",image:"pairs/p-17.png",image_rotated:"pairs/p-17-rotated.png",thumbnail:"pairs/s-p-17.png",price:"400"},
        {name:"Name",image:"pairs/p-18.png",image_rotated:"pairs/p-18-rotated.png",thumbnail:"pairs/s-p-18.png",price:"350"},
        {name:"Name",image:"pairs/p-19.png",image_rotated:"pairs/p-19-rotated.png",thumbnail:"pairs/s-p-19.png",price:"600"},
        {name:"Name",image:"pairs/p-20.png",image_rotated:"pairs/p-20-rotated.png",thumbnail:"pairs/s-p-20.png",price:"700"},
        {name:"Name",image:"pairs/p-21.png",image_rotated:"pairs/p-21-rotated.png",thumbnail:"pairs/s-p-21.png",price:"450"},
        {name:"Name",image:"pairs/p-22.png",image_rotated:"pairs/p-22-rotated.png",thumbnail:"pairs/s-p-22.png",price:"700"}
        ],
        grids:[
        {name:"Name",image:"grids/Body 01.png",thumbnail:"grids/Body-bg-1.png",price:"10000"},
        {name:"Name",image:"grids/Body 02.png",thumbnail:"grids/Body-bg-2.png",price:"14000"},
        {name:"Name",image:"grids/Body 03.png",thumbnail:"grids/Body-bg-3.png",price:"11000"},
        {name:"Name",image:"grids/Body 04.png",thumbnail:"grids/Body-bg-4.png",price:"11000"},
        {name:"Name",image:"grids/Body 05.png",thumbnail:"grids/Body-bg-5.png",price:"12000"},
        {name:"Name",image:"grids/Body 06.png",thumbnail:"grids/Body-bg-6.png",price:"15000"},
        {name:"Name",image:"grids/Body 07.png",thumbnail:"grids/Body-bg-7.png",price:"8000"},
        {name:"Name",image:"grids/Body 08.png",thumbnail:"grids/Body-bg-8.png",price:"6000"},
        {name:"Name",image:"grids/Body 09.png",thumbnail:"grids/Body-bg-9.png",price:"12000"},
        {name:"Name",image:"grids/Body 10.png",thumbnail:"grids/Body-bg-10.png",price:"16000"},
        {name:"Name",image:"grids/Body 11.png",thumbnail:"grids/Body-bg-11.png",price:"13000"},
        {name:"Name",image:"grids/Body 12.png",thumbnail:"grids/Body-bg-12.png",price:"9000"},
        {name:"Name",image:"grids/Body 13.png",thumbnail:"grids/Body-bg-13.png",price:"5000"},
        {name:"Name",image:"grids/Body 14.png",thumbnail:"grids/Body-bg-14.png",price:"8000"},
        {name:"Name",image:"grids/Body 15.png",thumbnail:"grids/Body-bg-15.png",price:"11000"},
        {name:"Name",image:"grids/Body 16.png",thumbnail:"grids/Body-bg-16.png",price:"9000"},
        {name:"Name",image:"grids/Body 17.png",thumbnail:"grids/Body-bg-17.png",price:"5500"},
        {name:"Name",image:"grids/Body 18.png",thumbnail:"grids/Body-bg-18.png",price:"9500"},
        {name:"Name",image:"grids/Body 19.png",thumbnail:"grids/Body-bg-19.png",price:"6500"},
        {name:"Name",image:"grids/Body 20.png",thumbnail:"grids/Body-bg-20.png",price:"8500"},
        {name:"Name",image:"grids/Body 21.png",thumbnail:"grids/Body-bg-21.png",price:"1200"},
        {name:"Name",image:"grids/Body 22.png",thumbnail:"grids/Body-bg-22.png",price:"8500"},
        {name:"Name",image:"grids/Body 23.png",thumbnail:"grids/Body-bg-23.png",price:"11500"},
        {name:"Name",image:"grids/Body 24.png",thumbnail:"grids/Body-bg-24.png",price:"1600"},
        {name:"Name",image:"grids/Body 25.png",thumbnail:"grids/Body-bg-25.png",price:"1500"},
        {name:"Name",image:"grids/Body 26.png",thumbnail:"grids/Body-bg-26.png",price:"1200"},
        {name:"Name",image:"grids/Body 27.png",thumbnail:"grids/Body-bg-27.png",price:"2500"},
        {name:"Name",image:"grids/Body 28.png",thumbnail:"grids/Body-bg-28.png",price:"1500"},
        {name:"Name",image:"grids/Body 29.png",thumbnail:"grids/Body-bg-29.png",price:"1600"},
        {name:"Name",image:"grids/Body 30.png",thumbnail:"grids/Body-bg-30.png",price:"1500"},
        {name:"Name",image:"grids/Body 31.png",thumbnail:"grids/Body-bg-31.png",price:"900"},
        {name:"Name",image:"grids/Body 32.png",thumbnail:"grids/Body-bg-32.png",price:"1500"},
        {name:"Name",image:"grids/Body 33.png",thumbnail:"grids/Body-bg-33.png",price:"1500"},
        {name:"Name",image:"grids/Body 34.png",thumbnail:"grids/Body-bg-34.png",price:"1200"},
        {name:"Name",image:"grids/Body 35.png",thumbnail:"grids/Body-bg-35.png",price:"1000"},
        {name:"Name",image:"grids/Body 36.png",thumbnail:"grids/Body-bg-36.png",price:"1200"},
        {name:"Name",image:"grids/Body 37.png",thumbnail:"grids/Body-bg-37.png",price:"890"},
        {name:"Name",image:"grids/Body 38.png",thumbnail:"grids/Body-bg-38.png",price:"1550"},
        {name:"Name",image:"grids/Body 39.png",thumbnail:"grids/Body-bg-39.png",price:"1200"},
        {name:"Name",image:"grids/Body 40.png",thumbnail:"grids/Body-bg-40.png",price:"1350"},
        {name:"Name",image:"grids/Body 41.png",thumbnail:"grids/Body-bg-41.png",price:"1900"},
        {name:"Name",image:"grids/Body 42.png",thumbnail:"grids/Body-bg-42.png",price:"1200"},
        {name:"Name",image:"grids/Body 43.png",thumbnail:"grids/Body-bg-43.png",price:"1500"},
        {name:"Name",image:"grids/Body 44.png",thumbnail:"grids/Body-bg-44.png",price:"1600"},
        {name:"Name",image:"grids/Body 45.png",thumbnail:"grids/Body-bg-45.png",price:"1070"},
        {name:"Name",image:"grids/Body 46.png",thumbnail:"grids/Body-bg-46.png",price:"1250"},
        {name:"Name",image:"grids/Body 47.png",thumbnail:"grids/Body-bg-47.png",price:"1890"},
        {name:"Name",image:"grids/Body 48.png",thumbnail:"grids/Body-bg-48.png",price:"1050"},
        {name:"Name",image:"grids/Body 49.png",thumbnail:"grids/Body-bg-49.png",price:"1000"},
        {name:"Name",image:"grids/Body 50.png",thumbnail:"grids/Body-bg-50.png",price:"1050"},
        {name:"Name",image:"grids/Body 51.png",thumbnail:"grids/Body-bg-51.png",price:"970"},
        {name:"Name",image:"grids/Body 52.png",thumbnail:"grids/Body-bg-52.png",price:"1050"},

        {name:"Name",image:"grids/Body 53.png",thumbnail:"grids/Body-bg-53.png",price:"1000"},
        {name:"Name",image:"grids/Body 54.png",thumbnail:"grids/Body-bg-54.png",price:"1150"},
        {name:"Name",image:"grids/Body 55.png",thumbnail:"grids/Body-bg-55.png",price:"950"},
        {name:"Name",image:"grids/Body 56.png",thumbnail:"grids/Body-bg-56.png",price:"690"},
        {name:"Name",image:"grids/Body 57.png",thumbnail:"grids/Body-bg-57.png",price:"2150"},
        {name:"Name",image:"grids/Body 58.png",thumbnail:"grids/Body-bg-58.png",price:"1950"},
        {name:"Name",image:"grids/Body 59.png",thumbnail:"grids/Body-bg-59.png",price:"1450"},


        ],
        achols:[
        {name:"Name",image:"achols/Achol 01.png",thumbnail:"achols/achol-bg-01.png",price:"1400"},
        {name:"Name",image:"achols/Achol 02.png",thumbnail:"achols/achol-bg-02.png",price:"1100"},
        {name:"Name",image:"achols/Achol 03.png",thumbnail:"achols/achol-bg-03.png",price:"1700"},
        {name:"Name",image:"achols/Achol 04.png",thumbnail:"achols/achol-bg-04.png",price:"1200"},
        {name:"Name",image:"achols/Achol 05.png",thumbnail:"achols/achol-bg-05.png",price:"1500"},
        {name:"Name",image:"achols/Achol 06.png",thumbnail:"achols/achol-bg-06.png",price:"1800"},
        {name:"Name",image:"achols/Achol 07.png",thumbnail:"achols/achol-bg-07.png",price:"1100"},
        {name:"Name",image:"achols/Achol 08.png",thumbnail:"achols/achol-bg-08.png",price:"1200"},
        {name:"Name",image:"achols/Achol 09.png",thumbnail:"achols/achol-bg-09.png",price:"1900"},
        {name:"Name",image:"achols/Achol 10.png",thumbnail:"achols/achol-bg-10.png",price:"2100"},
        {name:"Name",image:"achols/Achol 11.png",thumbnail:"achols/achol-bg-11.png",price:"1700"},
        {name:"Name",image:"achols/Achol 12.png",thumbnail:"achols/achol-bg-12.png",price:"1500"},
        {name:"Name",image:"achols/Achol 13.png",thumbnail:"achols/achol-bg-13.png",price:"1100"},
        {name:"Name",image:"achols/Achol 14.png",thumbnail:"achols/achol-bg-14.png",price:"1000"},
        {name:"Name",image:"achols/Achol 15.png",thumbnail:"achols/achol-bg-15.png",price:"700"},
        {name:"Name",image:"achols/Achol 16.png",thumbnail:"achols/achol-bg-16.png",price:"1200"},
        {name:"Name",image:"achols/Achol 17.png",thumbnail:"achols/achol-bg-17.png",price:"1400"},
        {name:"Name",image:"achols/Achol 18.png",thumbnail:"achols/achol-bg-18.png",price:"1500"},
        {name:"Name",image:"achols/Achol 19.png",thumbnail:"achols/achol-bg-19.png",price:"900"},
        {name:"Name",image:"achols/Achol 20.png",thumbnail:"achols/achol-bg-20.png",price:"2200"},
        {name:"Name",image:"achols/Achol 21.png",thumbnail:"achols/achol-bg-21.png",price:"1400"},
        {name:"Name",image:"achols/Achol 22.png",thumbnail:"achols/achol-bg-22.png",price:"1100"},
        {name:"Name",image:"achols/Achol 23.png",thumbnail:"achols/achol-bg-23.png",price:"1000"},
        {name:"Name",image:"achols/Achol 24.png",thumbnail:"achols/achol-bg-24.png",price:"1900"},
        {name:"Name",image:"achols/Achol 25.png",thumbnail:"achols/achol-bg-25.png",price:"2400"},
        {name:"Name",image:"achols/Achol 26.png",thumbnail:"achols/achol-bg-26.png",price:"2100"},
        {name:"Name",image:"achols/Achol 27.png",thumbnail:"achols/achol-bg-27.png",price:"700"},
        {name:"Name",image:"achols/Achol 28.png",thumbnail:"achols/achol-bg-28.png",price:"1200"},
        {name:"Name",image:"achols/Achol 29.png",thumbnail:"achols/achol-bg-29.png",price:"700"},
        {name:"Name",image:"achols/Achol 30.png",thumbnail:"achols/achol-bg-30.png",price:"1200"},
        {name:"Name",image:"achols/Achol 31.png",thumbnail:"achols/achol-bg-31.png",price:"1400"},
        {name:"Name",image:"achols/Achol 32.png",thumbnail:"achols/achol-bg-32.png",price:"890"},
        {name:"Name",image:"achols/Achol 33.png",thumbnail:"achols/achol-bg-33.png",price:"900"},
        {name:"Name",image:"achols/Achol 34.png",thumbnail:"achols/achol-bg-34.png",price:"1100"},
        {name:"Name",image:"achols/Achol 35.png",thumbnail:"achols/achol-bg-35.png",price:"1450"},
        {name:"Name",image:"achols/Achol 36.png",thumbnail:"achols/achol-bg-36.png",price:"1900"},
        {name:"Name",image:"achols/Achol 37.png",thumbnail:"achols/achol-bg-37.png",price:"1300"},
        {name:"Name",image:"achols/Achol 38.png",thumbnail:"achols/achol-bg-38.png",price:"1090"},
        {name:"Name",image:"achols/Achol 39.png",thumbnail:"achols/achol-bg-39.png",price:"790"},
        {name:"Name",image:"achols/Achol 40.png",thumbnail:"achols/achol-bg-40.png",price:"1600"},
        {name:"Name",image:"achols/Achol 41.png",thumbnail:"achols/achol-bg-41.png",price:"1500"},
        {name:"Name",image:"achols/Achol 42.png",thumbnail:"achols/achol-bg-42.png",price:"1090"},
        {name:"Name",image:"achols/Achol 43.png",thumbnail:"achols/achol-bg-43.png",price:"1050"},
        {name:"Name",image:"achols/Achol 44.png",thumbnail:"achols/achol-bg-44.png",price:"990"},
        {name:"Name",image:"achols/Achol 45.png",thumbnail:"achols/achol-bg-45.png",price:"1250"},
        {name:"Name",image:"achols/Achol 46.png",thumbnail:"achols/achol-bg-46.png",price:"1100"},
        {name:"Name",image:"achols/Achol 47.png",thumbnail:"achols/achol-bg-47.png",price:"1000"},
        {name:"Name",image:"achols/Achol 48.png",thumbnail:"achols/achol-bg-48.png",price:"1190"},
        {name:"Name",image:"achols/Achol 49.png",thumbnail:"achols/achol-bg-49.png",price:"990"},
        {name:"Name",image:"achols/Achol 50.png",thumbnail:"achols/achol-bg-50.png",price:"1200"},
        {name:"Name",image:"achols/Achol 51.png",thumbnail:"achols/achol-bg-51.png",price:"790"},
        {name:"Name",image:"achols/Achol 52.png",thumbnail:"achols/achol-bg-52.png",price:"1090"},
        {name:"Name",image:"achols/Achol 53.png",thumbnail:"achols/achol-bg-53.png",price:"1100"},

        {name:"Name",image:"achols/Achol 54.png",thumbnail:"achols/achol-bg-54.png",price:"1750"},
        {name:"Name",image:"achols/Achol 55.png",thumbnail:"achols/achol-bg-55.png",price:"1380"},
        {name:"Name",image:"achols/Achol 56.png",thumbnail:"achols/achol-bg-56.png",price:"970"},
        {name:"Name",image:"achols/Achol 57.png",thumbnail:"achols/achol-bg-57.png",price:"1600"},
        {name:"Name",image:"achols/Achol 58.png",thumbnail:"achols/achol-bg-58.png",price:"1960"},
        {name:"Name",image:"achols/Achol 59.png",thumbnail:"achols/achol-bg-59.png",price:"1800"},
        {name:"Name",image:"achols/Achol 60.png",thumbnail:"achols/achol-bg-60.png",price:"1050"},
        {name:"Name",image:"achols/Achol 61.png",thumbnail:"achols/achol-bg-51.png",price:"1700"},


        
        ],
      }
    },
    mounted(){
    },
    computed: {
      filteredSarees(){
        if(this.search === "") {
          return this.sarees;
        }
        let sarees = [];
        this.sarees.forEach(saree =>{
          let hasTag = saree.tag.some(t => t.indexOf(this.search.trim()) >=0);
          if(hasTag){
            sarees.push(saree);
          }
        });
        return sarees;      
      },
      totalPrice(){
        let yarn_count_price = parseInt(this.yarn_count.price);
        let background_price = parseInt(this.background.price);
        let pair_price = parseInt(this.pair.price);
        let achol_price = parseInt(this.achol.price);
        let grid_price = parseInt(this.grid.price);
        return yarn_count_price + background_price + pair_price + achol_price + grid_price;
      }
    },
    methods:{
      buildSaree(){
       mergeImages([ 
        { src: this.background.image, x: 0, y: 0 },
        { src: this.pair.image, x: 0, y: 0 },
        { src: this.grid.image, x: 0, y: 25 },
        { src: this.achol.image, x: 650, y: 25 },
        { src: this.pair.image_rotated, x: 0, y: 325 },
        ]).then(src => {
          this.saree_image = src;
        });
      },
      startCustomization(index){
        this.selectedSaree = index;
        this.selectBackground(this.sarees[index].background, false);
        this.selectPair(this.sarees[index].pair, false);
        this.selectAchol(this.sarees[index].achol, false);
        this.selectGrid(this.sarees[index].grid, false);
        this.isBuildingSaree = true;
        mergeImages([ 
          { src: this.background.image, x: 0, y: 0 },
          { src: this.pair.image, x: 0, y: 0 },
          { src: this.grid.image, x: 0, y: 25 },
          { src: this.achol.image, x: 650, y: 25 },
          { src: this.pair.image_rotated, x: 0, y: 325 },
          ]).then(src => {
            this.saree_image = src;
            window.scrollTo(0, 0);          
            window.scrollTo(0, 200);  
            this.hasSelectedSaree = true;
            this.isBuildingSaree = false;
          });

        },
        selectBackground(index, should_build = true){
          this.selectedBackground = index;
          this.background = this.backgrounds[index];
          if(should_build){
           this.buildSaree();
         }
       },
       selectPair(index, should_build = true){
        this.selectedPair = index;
        this.pair = this.pairs[index];
        if(should_build){
         this.buildSaree();
       }
     },
     selectAchol(index, should_build = true){
      this.selectedAchol = index;
      this.achol = this.achols[index];
      if(should_build){
       this.buildSaree();
     }
   },
   selectGrid(index, should_build = true){
    this.selectedGrid = index;
    this.grid = this.grids[index];
    if(should_build){
     this.buildSaree();
   }
 },
 nextStep(){
  this.step++;
}, 
previousStep(){
  if(this.step > 0){
    this.step--;
  }
},
downloadImage(){
  html2canvas(document.querySelector("#capture")).then(function(canvas) {
    var a = document.createElement('a');
          // toDataURL defaults to png, so we need to request a jpeg, then convert for file download.
          a.href = canvas.toDataURL("image/jpeg").replace("image/jpeg", "image/octet-stream");
          a.download = 'jamdani_'+Date.now()+'.jpg';
          a.click();
  });
},
loadMoreSarees(){
  let i =0;
  while(this.load_more_sarees.length > 0){
    if(i == 6){
      break;
    }
    this.sarees.push(this.load_more_sarees[0]);
    this.load_more_sarees.splice(0,1);
    i++;
  }
},
resetEveryThing(){
  this.hasSelectedSaree = false;
  this.selectedSaree = -1;
  this.step = 1;
  this.selectedBackground = -1;
  this.selectedPair = -1;
  this.selectedAchol = -1;
  this.selectedGrid = -1;
},
numberWithCommas(x){
  let parts = x.toString().split(".");
  parts[0] = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ",");
  return this.convertEnglishToBangla(parts.join("."));        
},
convertEnglishToBangla(input) {
  let numbers = {
   0:'০',
   1:'১',
   2:'২',
   3:'৩',
   4:'৪',
   5:'৫',
   6:'৬',
   7:'৭',
   8:'৮',
   9:'৯'
 };
 var output = [];
 for (var i = 0; i < input.length; ++i) {
  if (!isNaN(input[i])) {
    output.push(numbers[input[i]]);
  } else {
    output.push(input[i]);
  }
}
return output.join('');
}
}
}
</script>

<style scoped>

  body {
    font-family: 'kalpurushregular';  
    
  }
  .grid{
    width:650px;
    height:300px;
    background-image: none;
    background-repeat: repeat;
  }

  .achol{
    height:300px;
    background-image: none;
    background-size: cover; 
    background-repeat: no-repeat;
    background-position: center center; 
  }

  .pair{
    width:650px;
    height:30px;
    background-image: none;
  }
  .pair-rotated{
    transform: rotate(180deg);
  }

  .border-transparent{
    border: 1px solid transparent;
  }

  .border-red{
    border: 1px solid red;
  }

  .background-image{
    width:80px;
    height:80px;
  }
  .background-image:hover{
    cursor:pointer;
  }

  .sml-btn, .thm-btn.lft-icon.brd-btn.sml-btn, .thm-btn.brd-btn.sml-btn {
    font-size: 0.875rem;
    padding-top: 0.4rem;
    padding-bottom: .4rem;
  }
  .divider4 {
    height: 2px;
    margin: 0.188rem 0 0.5rem;
    background-color: var(--color10);
  }

  .p-2.border-red.background-image:hover {
    background: #25a94a;
    color: #f3d078;
    font-size: 150%;
    background-color: rgb(0 0 0 / 5%)!important;
    font-size: 150%;
    -webkit-transform: scale(1.4);
    -ms-transform: scale(1.4);
    transform: scale(1.4);
    transition: 1s ease;
    border-radius: 15%;
  }



  .p-2.border-transparent.background-image:hover {
    font-size: 150%;
    background-color:#155724  !important;
    font-size: 150%;
    -webkit-transform: scale(1.4);
    -ms-transform: scale(1.4);
    transform: scale(1.4);
    transition: 1s ease;
    border-radius: 15%;

  }
  th {
    text-align: left;
  }


  ::-webkit-scrollbar {
    height: 4px;
    width: 4px;
    border: 1px solid #d5d5d5;
  }


  .form-check {
    position: relative;
    display: block;
    padding: 10px 1.25rem;
  }

  @media only screen and (max-width: 768px) {
   .btn-w-100{
    width:100%
  }
}

.thm-btn.lft-icon.brd-btn {
  padding: 1.0625rem .875rem;
  padding-top: 1.0625rem;
  padding-bottom: 1.0625rem;
}

.selected-count-color{
  color: #e82277;
}

.active-step{
  color: #e82277;
  border-bottom: 2px dashed #000;
  

}

#menu-image > a > img {
  width: 120px;
  
}



/*search*/
#search-wrapper{
display: flex;
border: 1px solid rgba(0, 0, 0, 0.276);
align-items: stretch;
border-radius: 50px;
background-color: #fff;
overflow: hidden;
max-width: 100%;
box-shadow: 2px 1px 5px 1px rgba(0, 0, 0, 0.273);


}
#search{
    border:none;
    width:100%;
    font-size: 15px;
}
#search:focus{
    outline: none;
}
.search-icon{
    margin: 10px;
    color:rgba(0, 0, 0, 0.564);
}
#search-button{
    border:none;
    cursor: pointer;
    color:#fff;
    background-color:#1dbf73;
    padding:0px 10px;
}
</style>
