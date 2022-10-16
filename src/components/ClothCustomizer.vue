<template>
<div>
   <loading :active.sync="isBuildingSaree" :can-cancel="false" :is-full-page="true"/>
   <div class="row m-5" v-if="!hasSelectedSaree">
      <div class="col-lg-12">
         <input class="form-control mb-2" placeholder="আপনার পছন্দের শাড়ি খুঁজুন; লাল পার | লাল কাজ | ফুল বডি কাজ | হালকা কাজ" v-model="search"/>
         <div class="row">
            <div class="col-lg-4 mb-2 p-2" 
               v-for="(saree,index) in filteredSarees" 
               :key="index">
               <saree :saree="saree"  @click="startCustomization(saree.index)"/>
            </div>
            <h4 v-if="search !== '' && filteredSarees.length === 0" class="text-center" style="color:red">কোন কিছু খুঁজে পাওয়া যায়নি</h4>
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
         <div id="crumbs" >
            <ul>
               <li>
                  <a href="javascript:void(0);" @click="step = 1" :class="{'active-step':step === 1}">সুতার কাউন্ট</a>
               </li>
               <li>
                  <a href="javascript:void(0);" @click="step = 2" :class="{'active-step':step === 2}">কাপড়ের রং</a>
               </li>
               <li>
                  <a href="javascript:void(0);" @click="step = 3" :class="{'active-step':step === 3}">পাড়ের নকশা</a>
               </li>
               <li>
                  <a href="javascript:void(0);" @click="step = 4" :class="{'active-step':step === 4}">আঁচলের নকশা</a>
               </li>
               <li>
                  <a href="javascript:void(0);" @click="step = 5" :class="{'active-step':step === 5}">জমিনের নকশা </a>
               </li>
            </ul>
         </div>
   </div>
    <div class="col-lg-6">

         <div id="capture">
            <img v-if="saree_image !== null" :src="saree_image" style="transform:scale(0.90)"/>
         </div>
         <div class="w-100" >
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
            <div class="col-lg-12" v-if="step == 1">
               <h3>সুতার কাউন্ট নির্বাচন করুন</h3>
                <div class="row"> 
                    <div class="col-md-6 col-lg-4"  v-for="(yarn_type,index) in yarn_types" :key="index"> 
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
   <div class="modal" tabindex="-1" role="dialog" id="priceModal" style="top:90px;">
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
                        <th scope="row">সুতার কাউন্টের মূল্য</th>
                        <td><b> {{numberWithCommas(yarn_count.price/4)}} টাকা</b></td>
                     </tr>
                     <tr>
                        <th scope="row">কাপড়ের রং এর মূল্য</th>
                        <td><b> {{numberWithCommas(background.price)}} টাকা</b></td>
                     </tr>
                     <tr>
                        <th scope="row">আঁচলের  মূল্য</th>
                        <td><b> {{numberWithCommas(achol.price)}} টাকা</b></td>
                     </tr>
                     <tr>
                        <th scope="row">পাড়ের মূল্য</th>
                        <td><b> {{numberWithCommas(pair.price)}} টাকা</b></td>
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
        {tag:["হালকা কাজ", "সাদা", "নীল পার", "কালো কাজ", "নীল কাজ","halka kaj","sada","white","nil kaj", "nil par","blue", "asmani"], link:"sarees/27.jpg",background:5,pair:20,achol:11,grid:12,index:0},
        {tag:["হালকা কাজ", "নীল পার", "নীল কাজ","halka kaj","nil kaj", "nil par","blue", "asmani"], link:"sarees/28.jpg",background:22,pair:16,achol:21,grid:21,index:1},
        {tag:["ফুল বডি কাজ", "বাসন্তি", "কমলা", "গোলাপি পার", "গোলাপি কাজ","golapi","komola","orange","full body kaj", "pink", "holud", "yellow"], link:"sarees/29.jpg",background:24,pair:3,achol:2,grid:5,index:2},
        {tag:["হালকা কাজ", "গোলাপি পার", "গোলাপি কাজ","halka kaj","pink par","pink", "golapi"], link:"sarees/18.jpg",background:14,pair:2,achol:2,grid:1,index:3},
        {tag:["লাল কাজ","লাল আচল","মেজেন্ডা পার", "ফুল বডি কাজ","red","full body kaj", "lal"], link:"sarees/17.jpg",background:17,pair:13,achol:9,grid:1,index:4},
        {tag:["লাল কাজ", "নীল আচল","হালকা কাজ","blue achol","red kaj", "red", "nil", "lal" ], link:"sarees/21.jpg",background:4,pair:6,achol:1,grid:13,index:5},
        {tag:["হালকা কাজ","বাসন্তি","কমলা","সুরমা কাজ","orange","surma","bashonti","halka kaj", "pair", "holud", "yellow"], link:"sarees/22.jpg",background:26,pair:14,achol:19,grid:6,index:6},
        {tag:["ফুল বডি কাজ","গোলাপি পার", "গোলাপি কাজ", "বাসন্তি", "কমলা","full body kaj","golapi kaj","orange"], link:"sarees/23.jpg",background:37,pair:15,achol:22,grid:4,index:7},
        {tag:["হালকা কাজ","সুরমা কাজ","ash color","ash","surma", "halka kaj"], link:"sarees/24.jpg",background:10,pair:18,achol:19,grid:6,index:8},
        {tag:["ফুল বডি কাজ", "বাসন্তি", "কমলা", "komola", "red", "orange","full body kaj"], link:"sarees/25.jpg",background:40,pair:11,achol:15,grid:5,index:9},
        {tag:["লাল কাজ","লাল আচল", "ফুল বডি কাজ", "full body kaj", "red par", "red pair", "red achol", "lal"], link:"sarees/26.jpg",background:36,pair:3,achol:7,grid:1,index:10},
        {tag:["লাল কাজ", "বাসন্তি", "কমলা", "red","komola", "lal", "bashonti", "holud", "yellow"], link:"sarees/19.jpg",background:23,pair:11,achol:15,grid:5,index:11},
        {tag:["ফুল বডি কাজ", "full body kaj", "sada", "white", "lal par", "red"], link:"sarees/16.jpg",background:8,pair:0,achol:15,grid:16,index:12},
        {tag:["হালকা কাজ","sada", "white", "kalo", "black",  "halka kaj","কাল"], link:"sarees/2.jpg",background:1,pair:1,achol:1,grid:1,index:13},
        {tag:["লাল পার", "ফুল বডি কাজ", "full body kaj", "red", "lal","red achol", "লাল আচল", "full", "colorful" ], link:"sarees/7.jpg",background:6,pair:6,achol:6,grid:6,index:14},
        {tag:["ফুল বডি কাজ", "full body kaj", "ash","sada", "white body", "সাদা" ], link:"sarees/8.jpg",background:7,pair:7,achol:7,grid:7,index:15},
        {tag:["লাল পার", "sada", "white", "সাদা","ফুল বডি কাজ", "full body kaj", "lal", "red", "megenda", "মেজেন্ডা পার" ], link:"sarees/9.jpg",background:8,pair:8,achol:8,grid:8,index:16},
        {tag:["হালকা কাজ", "ask", "pink", "halka kaj"], link:"sarees/11.jpg",background:10,pair:10,achol:10,grid:10,index:17},
        {tag:["লাল পার","red par", "ash","chai", "karukaj"], link:"sarees/12.jpg",background:11,pair:11,achol:11,grid:11,index:18},
        {tag:["ফুল বডি কাজ", "sada", "white body", "সাদা", "full body kaj", "green" , "sobuj", "sabuj", "blue", "nil"], link:"sarees/13.jpg",background:12,pair:12,achol:12,grid:12,index:19},
        {tag:["ফুল বডি কাজ", "full body kaj", "green" , "sobuj", "sabuj"], link:"sarees/14.jpg",background:13,pair:13,achol:13,grid:13,index:20},


        ],
        load_more_sarees:[
        {tag:["ফুল বডি কাজ", "sada", "white body", "সাদা", "full body kaj", "red" , "red kaj", "লাল পার"], link:"sarees/30.jpg",background:5,pair:0,achol:0,grid:5,index:21},
        {tag:["হালকা কাজ", "golapi", "গোলাপি পার", "গোলাপি কাজ", "off white"], link:"sarees/31.jpg",background:13,pair:3,achol:2,grid:5,index:22},
        {tag:["লাল কাজ", "orange", "কমলা", "বাসন্তি"], link:"sarees/32.jpg",background:38,pair:11,achol:0,grid:3,index:23},
        {tag:["ফুল বডি কাজ", "full body kaj", "sada", "white", "lal par", "red"], link:"sarees/33.jpg",background:10,pair:6,achol:7,grid:5,index:24},
        {tag:["ফুল বডি কাজ", "ash", "sada", "white", "full body kaj", "surma", "chai color"], link:"sarees/34.jpg",background:2,pair:15,achol:19,grid:7,index:25},
        {tag:["হালকা কাজ", "golapi", "গোলাপি পার", "গোলাপি কাজ", "halka kaj"], link:"sarees/35.jpg",background:3,pair:3,achol:2,grid:6,index:26},
        {tag:["ফুল বডি কাজ","full body kaj", "sobuj", "green", "colorful"], link:"sarees/20.jpg",background:30,pair:3,achol:14,grid:13,index:27},
        {tag:["ফুল বডি কাজ", "full body kaj", "গোলাপি পার", "গোলাপি কাজ", "sada", "white"], link:"sarees/15.jpg",background:14,pair:14,achol:14,grid:14,index:28},
        {tag:["লাল কাজ", "গোলাপি কাজ", "halka kaj", "full lota"], link:"sarees/11.jpg",background:10,pair:10,achol:10,grid:10,index:29},
        {tag:["লাল পার", "red par", "ash", "chai", "full noksha"], link:"sarees/12.jpg",background:11,pair:11,achol:11,grid:11,index:30},
        {tag:["লাল পার","red par"], link:"sarees/13.jpg",background:12,pair:12,achol:12,grid:12,index:31},        
        {tag:["লাল পার"], link:"sarees/1.jpg",background:0,pair:0,achol:0,grid:0,index:32},
        {tag:["ফুল বডি কাজ", "kalo", "black", "blue", "কাল", "full" ,"গোলাপি পার", "গোলাপি কাজ", "colorful", "golapi"], link:"sarees/3.jpg",background:2,pair:2,achol:2,grid:2,index:33},
        {tag:["ফুল বডি কাজ", "full body kaj", "গোলাপি পার", "গোলাপি কাজ", "full", "colorful", "golapi"], link:"sarees/4.jpg",background:3,pair:3,achol:3,grid:3,index:34},
        {tag:["ফুল বডি কাজ", "kalo", "black", "blue", "কাল"], link:"sarees/5.jpg",background:4,pair:4,achol:4,grid:4,index:35},
        {tag:["ফুল বডি কাজ", "sada", "white", "সাদা","green","সবুজ কাজ", "লাল", "red"], link:"sarees/6.jpg",background:5,pair:5,achol:5,grid:5,index:36},
        {tag:["লাল পার", "red par", "full", "golapi","গোলাপি কাজ", "sada", "white", "সাদা"], link:"sarees/9.jpg",background:8,pair:8,achol:8,grid:8,index:37},
        {tag:["ফুল বডি কাজ","green","সবুজ কাজ","sada", "white", "blue", "সাদা" ], link:"sarees/10.jpg",background:9,pair:9,achol:9,grid:9,index:38},

        ],
        backgrounds:[
        {image:"backgrounds/body850-1.png",price: "2000"},
        {image:"backgrounds/body850-2.png",price: "2150"},
        {image:"backgrounds/body850-3.png",price: "2300"},
        {image:"backgrounds/body850-4.png",price: "4000"},
        {image:"backgrounds/body850-5.png",price: "3200"},
        {image:"backgrounds/body850-6.png",price: "1800"},
        {image:"backgrounds/body850-7.png",price: "1740"},
        {image:"backgrounds/body850-8.png",price: "5000"},
        {image:"backgrounds/body850-9.png",price: "2490"},
        {image:"backgrounds/body850-11.png",price: "2300"},
        {image:"backgrounds/body850-12.png",price: "5200"},
        {image:"backgrounds/body850-13.png",price: "2900"},
        {image:"backgrounds/body850-14.png",price: "3800"},
        {image:"backgrounds/body850-15.png",price: "9000"},
        {image:"backgrounds/body850-16.png",price: "1200"},
        {image:"backgrounds/body850-17.png",price: "2990"},
        {image:"backgrounds/body850-18.png",price: "2300"},
        {image:"backgrounds/body850-19.png",price: "3100"},
        {image:"backgrounds/body850-20.png",price: "3240"},
        {image:"backgrounds/body850-21.png",price: "2400"},
        {image:"backgrounds/body850-22.png",price: "2550"},
        {image:"backgrounds/body850-23.png",price: "2700"},
        {image:"backgrounds/body850-24.png",price: "2800"},
        {image:"backgrounds/body850-25.png",price: "3450"},
        {image:"backgrounds/body850-26.png",price: "2500"},
        {image:"backgrounds/body850-27.png",price: "1200"},
        {image:"backgrounds/body850-28.png",price: "2100"},
        {image:"backgrounds/body850-29.png",price: "1900"},
        {image:"backgrounds/body850-30.png",price: "2000"},
        {image:"backgrounds/body850-31.png",price: "1400"},
        {image:"backgrounds/body850-32.png",price: "1300"},
        {image:"backgrounds/body850-33.png",price: "1200"},
        {image:"backgrounds/body850-34.png",price: "1100"},
        {image:"backgrounds/body850-35.png",price: "850"},
        {image:"backgrounds/body850-36.png",price: "6000"},
        {image:"backgrounds/body850-37.png",price: "2000"},
        {image:"backgrounds/body850-38.png",price: "3000"},
        {image:"backgrounds/body850-39.png",price: "2100"},
        {image:"backgrounds/body850-40.png",price: "2200"},
        {image:"backgrounds/body850-41.png",price: "2300"},
        {image:"backgrounds/body850-42.png",price: "1800"},
        ], 
        pairs:[
        {image:"pairs/p-001.png",image_rotated:"pairs/p-001-rotated.png",thumbnail:"pairs/s-p-001.png",price:"600"},
        {image:"pairs/p-002.png",image_rotated:"pairs/p-002-rotated.png",thumbnail:"pairs/s-p-002.png",price:"450"},
        {image:"pairs/p-003.png",image_rotated:"pairs/p-003-rotated.png",thumbnail:"pairs/s-p-003.png",price:"700"},
        {image:"pairs/p-005.png",image_rotated:"pairs/p-005-rotated.png",thumbnail:"pairs/s-p-005.png",price:"1200"},
        {image:"pairs/p-006.png",image_rotated:"pairs/p-006-rotated.png",thumbnail:"pairs/s-p-006.png",price:"990"},
        {image:"pairs/p-007.png",image_rotated:"pairs/p-007-rotated.png",thumbnail:"pairs/s-p-007.png",price:"320"},
        {image:"pairs/p-008.png",image_rotated:"pairs/p-008-rotated.png",thumbnail:"pairs/s-p-008.png",price:"590"},
        {image:"pairs/p-009.png",image_rotated:"pairs/p-009-rotated.png",thumbnail:"pairs/s-p-009.png",price:"800"},
        {image:"pairs/p-0010.png",image_rotated:"pairs/p-0010-rotated.png",thumbnail:"pairs/s-p-0010.png",price:"900"},
        {image:"pairs/p-0011.png",image_rotated:"pairs/p-0011-rotated.png",thumbnail:"pairs/s-p-0011.png",price:"450"},
        {image:"pairs/p-0012.png",image_rotated:"pairs/p-0012-rotated.png",thumbnail:"pairs/s-p-0012.png",price:"620"},
        {image:"pairs/p-0013.png",image_rotated:"pairs/p-0013-rotated.png",thumbnail:"pairs/s-p-0013.png",price:"480"},
        {image:"pairs/p-0014.png",image_rotated:"pairs/p-0014-rotated.png",thumbnail:"pairs/s-p-0014.png",price:"510"},
        {image:"pairs/p-0015.png",image_rotated:"pairs/p-0015-rotated.png",thumbnail:"pairs/s-p-0015.png",price:"550"},
        {image:"pairs/p-16.png",image_rotated:"pairs/p-16-rotated.png",thumbnail:"pairs/s-p-16.png",price:"450"},
        {image:"pairs/p-17.png",image_rotated:"pairs/p-17-rotated.png",thumbnail:"pairs/s-p-17.png",price:"400"},
        {image:"pairs/p-18.png",image_rotated:"pairs/p-18-rotated.png",thumbnail:"pairs/s-p-18.png",price:"350"},
        {image:"pairs/p-19.png",image_rotated:"pairs/p-19-rotated.png",thumbnail:"pairs/s-p-19.png",price:"600"},
        {image:"pairs/p-20.png",image_rotated:"pairs/p-20-rotated.png",thumbnail:"pairs/s-p-20.png",price:"700"},
        {image:"pairs/p-21.png",image_rotated:"pairs/p-21-rotated.png",thumbnail:"pairs/s-p-21.png",price:"450"},
        {image:"pairs/p-22.png",image_rotated:"pairs/p-22-rotated.png",thumbnail:"pairs/s-p-22.png",price:"700"}
        ],
        grids:[
        {image:"grids/b-001.png",thumbnail:"grids/s-b-001.png",price:"12000"},
        {image:"grids/b-002.png",thumbnail:"grids/s-b-002.png",price:"5000"},
        {image:"grids/b-003.png",thumbnail:"grids/s-b-003.png",price:"2000"},
        {image:"grids/b-004.png",thumbnail:"grids/s-b-004.png",price:"4500"},
        {image:"grids/b-005.png",thumbnail:"grids/s-b-005.png",price:"5600"},
        {image:"grids/b-006.png",thumbnail:"grids/s-b-006.png",price:"2500"},
        {image:"grids/b-007.png",thumbnail:"grids/s-b-007.png",price:"7400"},
        {image:"grids/b-008.png",thumbnail:"grids/s-b-008.png",price:"5000"},
        {image:"grids/b-009.png",thumbnail:"grids/s-b-009.png",price:"12000"},
        {image:"grids/b-0010.png",thumbnail:"grids/s-b-0010.png",price:"6500"},
        {image:"grids/b-0011.png",thumbnail:"grids/s-b-0011.png",price:"2000"},
        {image:"grids/b-0012.png",thumbnail:"grids/s-b-0012.png",price:"3500"},
        {image:"grids/b-0013.png",thumbnail:"grids/s-b-0013.png",price:"6700"},
        {image:"grids/b-0014.png",thumbnail:"grids/s-b-0014.png",price:"5900"},
        {image:"grids/b-0015.png",thumbnail:"grids/s-b-0015.png",price:"1200"},
        {image:"grids/b-0016.png",thumbnail:"grids/s-b-0016.png",price:"1900"},
        {image:"grids/b-0017.png",thumbnail:"grids/s-b-0017.png",price:"1200"},
        {image:"grids/b-0018.png",thumbnail:"grids/s-b-0018.png",price:"1250"},
        {image:"grids/b-0019.png",thumbnail:"grids/s-b-0019.png",price:"3500"},
        {image:"grids/b-20.png",thumbnail:"grids/s-b-20.png",price:"10000"},
        {image:"grids/b-21.png",thumbnail:"grids/s-b-21.png",price:"1300"},
        {image:"grids/b-22.png",thumbnail:"grids/s-b-22.png",price:"1250"},
        {image:"grids/b-23.png",thumbnail:"grids/s-b-23.png",price:"1200"},
        {image:"grids/b-24.png",thumbnail:"grids/s-b-24.png",price:"6000"},
        {image:"grids/b-25.png",thumbnail:"grids/s-b-25.png",price:"2000"},
        {image:"grids/b-26.png",thumbnail:"grids/s-b-26.png",price:"3500"},
        {image:"grids/b-27.png",thumbnail:"grids/s-b-27.png",price:"4500"}
        ],
        achols:[
        {image:"achols/a-01.png",thumbnail:"achols/s-a-01.png",price:"1000"},
        {image:"achols/a-02.png",thumbnail:"achols/s-a-02.png",price:"1500"},
        {image:"achols/a-03.png",thumbnail:"achols/s-a-03.png",price:"3000"},
        {image:"achols/a-04.png",thumbnail:"achols/s-a-04.png",price:"3500"},
        {image:"achols/a-05.png",thumbnail:"achols/s-a-05.png",price:"6500"},
        {image:"achols/a-06.png",thumbnail:"achols/s-a-06.png",price:"1550"},
        {image:"achols/a-07.png",thumbnail:"achols/s-a-07.png",price:"2100"},
        {image:"achols/a-08.png",thumbnail:"achols/s-a-08.png",price:"12000"},
        {image:"achols/a-09.png",thumbnail:"achols/s-a-09.png",price:"1300"},
        {image:"achols/a-10.png",thumbnail:"achols/s-a-10.png",price:"1250"},
        {image:"achols/a-11.png",thumbnail:"achols/s-a-11.png",price:"2100"},
        {image:"achols/a-12.png",thumbnail:"achols/s-a-12.png",price:"3500"},
        {image:"achols/a-13.png",thumbnail:"achols/s-a-13.png",price:"430"},
        {image:"achols/a-14.png",thumbnail:"achols/s-a-14.png",price:"6500"},
        {image:"achols/a-15.png",thumbnail:"achols/s-a-15.png",price:"7300"},
        {image:"achols/a-16.png",thumbnail:"achols/s-a-16.png",price:"6300"},
        {image:"achols/a-17.png",thumbnail:"achols/s-a-17.png",price:"5500"},
        {image:"achols/a-18.png",thumbnail:"achols/s-a-18.png",price:"3500"},
        {image:"achols/a-19.png",thumbnail:"achols/s-a-19.png",price:"4800"},
        {image:"achols/a-20.png",thumbnail:"achols/s-a-20.png",price:"9000"},
        {image:"achols/a-21.png",thumbnail:"achols/s-a-21.png",price:"12000"},
        {image:"achols/a-22.png",thumbnail:"achols/s-a-22.png",price:"2500"},
        {image:"achols/a-23.png",thumbnail:"achols/s-a-23.png",price:"3200"},
        {image:"achols/a-24.png",thumbnail:"achols/s-a-24.png",price:"5000"},
        {image:"achols/a-25.png",thumbnail:"achols/s-a-25.png",price:"6800"}
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
            window.scrollTo(0, 190);  
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
    curson:pointer;
  }

  #crumbs {
    text-align: center;
  }
  #crumbs h1 {
    padding: 0 0 30px;
    text-transform: uppercase;
    font-size: 0.9rem;
    font-weight: 600;
    letter-spacing: 0.01rem;
    color: #8093A7;
  }
  #crumbs ul {
    list-style: none;
    display: inline-table;
  }
  #crumbs ul li {
    display: inline;
  }
  #crumbs ul li a:after {
    content: "";
    border-top: 40px solid transparent;
    border-bottom: 40px solid transparent;
    border-left: 40px solid #cfbb81;
    position: absolute;
    right: -39px;
    top: 0;
    z-index: 1;
    background-color: transparent;
    
  }
  #crumbs ul li a:before {
    content: "";
    border-top: 40px solid transparent;
    border-bottom: 40px solid transparent;
    border-left: 40px solid #fff;
    position: absolute;
    left: 0;
    top: 0;
  }

  #crumbs ul li:first-child a {
    border-top-left-radius: 10px;
    border-bottom-left-radius: 10px;
  }

  #crumbs ul li:first-child a:before {
    display: none;
  }

  #crumbs ul li:last-child a {
    padding-right: 40px;
    border-top-right-radius: 10px;
    border-bottom-right-radius: 10px;
  }

  #crumbs ul li:last-child a:after {
    display: none;
  }

  #crumbs ul li a:hover {
    background: #25a94a;
    color: #f3d078;
    font-size: 150%;
  }

  #crumbs ul li a {
    display: block;
    float: left;
    height: 81px;
    background: #cfbb81;
    text-align: center;
    padding: 30px 20px 0 60px;
    position: relative;
    margin: 0 10px 0 0;
    font-size: 20px;
    text-decoration: none;
    color: #fff;
  }

  #crumbs ul li a:hover:after {
    border-left: 40px solid #25a94a;
    right: -39px;
    z-index: 1;
  }
  #crumbs ul li a.active-step{
    background-color:#25a94a;
  }

  #crumbs ul li a.active-step.active-step:after{
    border-left-color:#25a94a;

  }

  #app {
    margin-top: 20px!important;

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
  color: green;
}

</style>
