<template>
  <div class="container">
    <div class="row" v-if="!hasSelectedSaree">
        <div class="col-lg-12">
          <input class="form-control mb-2" placeholder="শাড়ী খুঁজুন" v-model="search"/>
          <div class="row">
            <div class="col-lg-4 mb-2 p-2" 
                 v-for="(saree,index) in filteredSarees" 
                 :key="index">
              <saree :saree="saree"  @click="startCustomization(index)"/>
            </div>
          </div>
          <div class="w-100">
            <button class="thm-btn mt-30 md-btn lft-icon fill-btn mr-5" 
                     href="javascript:void(0);"
                     @click="loadMoreSarees" 
                     v-if="load_more_sarees.length > 0"> লোড করুন<span></span>
            </button>
          </div>
        </div>
    </div>
    <div class="row" v-if="hasSelectedSaree">
        <div class="col-lg-12">
          <div id="crumbs" class="mb-5">
            <ul>
              <li>
                <a href="javascript:void(0);" @click="step = 1" :class="{'active-step':step === 1}">সুতা নির্বাচন</a>
              </li>
              <li>
                <a href="javascript:void(0);" @click="step = 2" :class="{'active-step':step === 2}">রঙ নির্বাচন</a>
              </li>
              <li>
                <a href="javascript:void(0);" @click="step = 3" :class="{'active-step':step === 3}">পাড় নির্বাচন</a>
              </li>
              <li>
                <a href="javascript:void(0);" @click="step = 4" :class="{'active-step':step === 4}">আঁচল নির্বাচন</a>
              </li>
              <li>
                <a href="javascript:void(0);" @click="step = 5" :class="{'active-step':step === 5}">জমিন নির্বাচন</a>
              </li>
            </ul>
          </div>
        </div>
        <div class="class-lg-12">
          <div id="capture" class="parent parent-scale">
            <img :src="background" class="image1"/>
            <img :src="grid" class="image2"/>
            <img :src="achol" class="image3"/>
            <img :src="pair" class="image4"/>
            <img :src="pair" class="image5 pair-rotated"/>
          </div> 
        </div>
        <div class="col-lg-12">
          <div class="w-100"> 
            <button class="thm-btn mt-30 md-btn lft-icon fill-btn mr-5" 
                     href="javascript:void(0);"
                     @click="resetEveryThing">
                 পুনরায় শাড়ী পছন্দ করুণ <span></span>
              </button>            
              <button class="thm-btn mt-30 md-btn lft-icon fill-btn mb-5" 
                     href="javascript:void(0);"
                     @click="downloadImage">
                 ডাউনলোড করুন<span></span>
              </button>
          </div>
        </div>
        <div class="col-lg-12" v-if="step == 1">
            <h3>সুতার কাউন্ট নির্বাচন করুন</h3>
            <div class="d-inline-flex p-2">
              <div class="form-check">
                <input type="radio" v-model="yarn_count" name="yarn_count" value="70" class="mr-2"/>
                <label class="form-check-label">৭০ কাউন্ট সুতা</label>
              </div>
              <div class="form-check">
                <input type="radio" v-model="yarn_count" name="yarn_count" value="84" class="mr-2"/> 
                <label class="form-check-label">৮৪ কাউন্ট সুতা</label>
              </div>
              <div class="form-check">
                <input type="radio" v-model="yarn_count" name="yarn_count" value="100" class="mr-2"/>
                <label>১০০ কাউন্ট সুতা</label>
              </div>
            </div>
            <div class="w-100">
                <button class="thm-btn mt-30 md-btn lft-icon fill-btn" 
                   href="javascript:void(0);" 
                   @click="nextStep"
                   title="">
                    পরবর্তী ধাপ <i class="flaticon-trajectory"></i><span></span>
                </button>
            </div>
        </div>
        <div class="col-lg-12" v-if="step == 2">
            <div class="row">
              <div  class="col-lg-12" >
                <h3>রঙ নির্বাচন করুন</h3>
                <img :src="`${base_url}/${background}`" 
                     v-for="(background,index) in backgrounds"  
                     :key="index" 
                     class="p-2"
                     @click="selectBackground(index)"
                     style="width:150px;height:150px;"
                     :class="[index ===  selectedBackground? 'border-red' : 'border-transparent','background-image']"/> 
                <br/> 
                <div class="w-100">
                    <button class="thm-btn mt-30 md-btn lft-icon fill-btn mr-5" 
                       href="javascript:void(0);"
                       @click="previousStep" 
                       title="">
                        <i class="flaticon-arrow-pointing-to-left"></i> পূর্ববর্তী ধাপ<span></span>
                    </button>
                    <button class="thm-btn mt-30 md-btn lft-icon fill-btn" 
                       href="javascript:void(0);" 
                       @click="nextStep"
                       :disabled="selectedBackground === -1"
                       title="">
                        পরবর্তী ধাপ <i class="flaticon-trajectory"></i><span></span>
                    </button>
                </div>   
              </div>
            </div>
        </div>
        <div class="col-lg-12" v-if="step == 3">
            <div class="row">
              <div  class="col-lg-12" >
                <h3>শাড়ীর পাড় নির্বাচন করুন</h3>
                <img :src="`${base_url}/${pair}`" 
                     v-for="(pair,index) in pair_thumbnails"  
                     :key="index" 
                     class="p-2"
                     @click="selectPair(index)"
                     :class="[index ===  selectedPair? 'border-red' : 'border-transparent','background-image']"/> 
                <br/> 
                <div class="w-100"> 
                  <button class="thm-btn mt-30 md-btn lft-icon fill-btn mr-5" 
                     href="javascript:void(0);"
                     @click="previousStep" 
                     title="">
                      <i class="flaticon-arrow-pointing-to-left"></i> পূর্ববর্তী ধাপ<span></span>
                  </button>
                  <button class="thm-btn mt-30 md-btn lft-icon fill-btn" 
                     href="javascript:void(0);" 
                     @click="nextStep"
                     :disabled="selectedPair === -1"
                     title="">
                      পরবর্তী ধাপ <i class="flaticon-trajectory"></i><span></span>
                  </button>
                </div>    
              </div>
            </div>
        </div>
        <div class="col-lg-12" v-if="step == 4">
            <div class="row">
              <div  class="col-lg-12" >
                <h3>শাড়ীর আচল নির্বাচন করুন</h3>
                <img :src="`${base_url}/${achol}`" 
                     v-for="(achol,index) in achols_thumbnails"  
                     :key="index" 
                     class="p-2"
                     @click="selectAchol(index)"
                     style="width:150px;height:150px;"
                     :class="[index ===  selectedAchol? 'border-red' : 'border-transparent','background-image']"/> 
                <br/>
                <div class="w-100"> 
                  <button class="thm-btn mt-30 md-btn lft-icon fill-btn  mr-5" 
                     href="javascript:void(0);"
                     @click="previousStep" 
                     title="">
                      <i class="flaticon-arrow-pointing-to-left"></i> পূর্ববর্তী ধাপ<span></span>
                  </button>
                  <button class="thm-btn mt-30 md-btn lft-icon fill-btn" 
                     href="javascript:void(0);" 
                     @click="nextStep"
                     :disabled="selectedAchol === -1"
                     title="">
                      পরবর্তী ধাপ <i class="flaticon-trajectory"></i><span></span>
                  </button> 
                </div>    
              </div>
            </div>
        </div>
        <div class="col-lg-12" v-if="step == 5">
            <div class="row">
              <div  class="col-lg-12" >
              <h3>শাড়ীর জমিন নির্বাচন করুন</h3>
                <img :src="`${base_url}/${grid}`" 
                     v-for="(grid,index) in grids_thumbnails"  
                     :key="index" 
                     class="p-2"
                     @click="selectGrid(index)"
                     style="width:180px;height:180px;"
                     :class="[index ===  selectedGrid? 'border-red' : 'border-transparent','background-image']"/> 
                <br/>
                <div class="w-100"> 
                  <button class="thm-btn mt-30 md-btn lft-icon fill-btn mr-5" 
                     href="javascript:void(0);"
                     @click="previousStep" 
                     title="">
                      <i class="flaticon-arrow-pointing-to-left"></i> পূর্ববর্তী ধাপ<span></span>
                  </button>
                </div>     
              </div>
            </div>
        </div>
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas';
import Saree from './Saree';
export default {
  name: 'ClothCustomizer',
  components:{
    Saree
  },
  data(){
    return{
      base_url:"https://imjalal.com/web-jamdani",
      background: 'none',
      pair: 'none',
      achol:'none',
      grid: 'none',
      hasSelectedSaree:false,
      selectedSaree:-1,
      step:1,
      selectedBackground:-1,
      selectedPair:-1,
      selectedAchol:-1,
      selectedGrid:-1,
      yarn_count:"70",
      search:"",
      sarees:[
        {tag:"red", link:"sarees/1.jpg",background:0,pair:0,achol:0,grid:0},
        {tag:"", link:"sarees/2.jpg",background:1,pair:1,achol:1,grid:1},
        {tag:"", link:"sarees/3.jpg",background:2,pair:2,achol:2,grid:2},
        {tag:"", link:"sarees/4.jpg",background:3,pair:3,achol:3,grid:3},
        {tag:"", link:"sarees/5.jpg",background:4,pair:4,achol:4,grid:4},
        {tag:"red", link:"sarees/6.jpg",background:5,pair:5,achol:5,grid:5},
        {tag:"", link:"sarees/7.jpg",background:6,pair:6,achol:6,grid:6},
        {tag:"", link:"sarees/8.jpg",background:7,pair:7,achol:7,grid:7},
        {tag:"red", link:"sarees/9.jpg",background:8,pair:8,achol:8,grid:8},
        {tag:"", link:"sarees/10.jpg",background:9,pair:9,achol:9,grid:9},
        {tag:"", link:"sarees/11.jpg",background:10,pair:10,achol:10,grid:10},
        {tag:"", link:"sarees/12.jpg",background:11,pair:11,achol:11,grid:11},
        {tag:"", link:"sarees/13.jpg",background:12,pair:12,achol:12,grid:12},
        {tag:"", link:"sarees/14.jpg",background:13,pair:13,achol:13,grid:13},
        {tag:"", link:"sarees/15.jpg",background:14,pair:14,achol:14,grid:14},
      ],
      load_more_sarees:[

      ],
      backgrounds:[
        "backgrounds/body850-1.png",
        "backgrounds/body850-2.png",
        "backgrounds/body850-3.png",
        "backgrounds/body850-4.png",
        "backgrounds/body850-5.png",
        "backgrounds/body850-6.png",
        "backgrounds/body850-7.png",
        "backgrounds/body850-8.png",
        "backgrounds/body850-9.png",
        "backgrounds/body850-11.png",
        "backgrounds/body850-12.png",
        "backgrounds/body850-13.png",
        "backgrounds/body850-14.png",
        "backgrounds/body850-15.png",
        "backgrounds/body850-16.png",
        "backgrounds/body850-17.png",
        "backgrounds/body850-18.png",
        "backgrounds/body850-19.png",
        "backgrounds/body850-20.png",
        "backgrounds/body850-21.png",
        "backgrounds/body850-22.png",
        "backgrounds/body850-23.png",
        "backgrounds/body850-24.png",
        "backgrounds/body850-25.png",
        "backgrounds/body850-26.png",
        "backgrounds/body850-27.png",
        "backgrounds/body850-28.png",
        "backgrounds/body850-29.png",
        "backgrounds/body850-30.png",
        "backgrounds/body850-31.png",
        "backgrounds/body850-32.png",
        "backgrounds/body850-33.png",
        "backgrounds/body850-34.png",
        "backgrounds/body850-35.png",
        "backgrounds/body850-36.png",
        "backgrounds/body850-37.png",
        "backgrounds/body850-38.png",
        "backgrounds/body850-39.png",
        "backgrounds/body850-40.png",
        "backgrounds/body850-41.png",
        "backgrounds/body850-42.png",
      ], 
      pairs:[
        "pairs/p-001.png",
        "pairs/p-002.png",
        "pairs/p-003.png",
        "pairs/p-005.png",
        "pairs/p-006.png",
        "pairs/p-007.png",
        "pairs/p-008.png",
        "pairs/p-009.png",
        "pairs/p-0010.png",
        "pairs/p-0011.png",
        "pairs/p-0012.png",
        "pairs/p-0013.png",
        "pairs/p-0014.png",
        "pairs/p-0015.png",
      ],
      pair_thumbnails:[
        "pairs/s-p-001.png",
        "pairs/s-p-002.png",
        "pairs/s-p-003.png",
        "pairs/s-p-005.png",
        "pairs/s-p-006.png",
        "pairs/s-p-007.png",
        "pairs/s-p-008.png",
        "pairs/s-p-009.png",
        "pairs/s-p-0010.png",
        "pairs/s-p-0011.png",
        "pairs/s-p-0012.png",
        "pairs/s-p-0013.png",
        "pairs/s-p-0014.png",
        "pairs/s-p-0015.png",
      ],
      grids:[
        "grids/b-001.png",
        "grids/b-002.png",
        "grids/b-003.png",
        "grids/b-004.png",
        "grids/b-005.png",
        "grids/b-006.png",
        "grids/b-007.png",
        "grids/b-008.png",
        "grids/b-009.png",
        "grids/b-0010.png",
        "grids/b-0011.png",
        "grids/b-0012.png",
        "grids/b-0013.png",
        "grids/b-0014.png",
        "grids/b-0015.png",
        "grids/b-0016.png",
        "grids/b-0017.png",
      ],
      grids_thumbnails:[
        "grids/s-b-001.png",
        "grids/s-b-002.png",
        "grids/s-b-003.png",
        "grids/s-b-004.png",
        "grids/s-b-005.png",
        "grids/s-b-006.png",
        "grids/s-b-007.png",
        "grids/s-b-008.png",
        "grids/s-b-009.png",
        "grids/s-b-0010.png",
        "grids/s-b-0011.png",
        "grids/s-b-0012.png",
        "grids/s-b-0013.png",
        "grids/s-b-0014.png",
        "grids/s-b-0015.png",
        "grids/s-b-0016.png",
        "grids/s-b-0017.png",
      ],
      achols:[
        "achols/a-01.png",
        "achols/a-02.png",
        "achols/a-03.png",
        "achols/a-04.png",
        "achols/a-05.png",
        "achols/a-06.png",
        "achols/a-07.png",
        "achols/a-08.png",
        "achols/a-09.png",
        "achols/a-10.png",
        "achols/a-11.png",
        "achols/a-12.png",
        "achols/a-13.png",
        "achols/a-14.png",
        "achols/a-15.png",
        "achols/a-16.png",
      ],
      achols_thumbnails:[
        "achols/s-a-01.png",
        "achols/s-a-02.png",
        "achols/s-a-03.png",
        "achols/s-a-04.png",
        "achols/s-a-05.png",
        "achols/s-a-06.png",
        "achols/s-a-07.png",
        "achols/s-a-08.png",
        "achols/s-a-09.png",
        "achols/s-a-10.png",
        "achols/s-a-11.png",
        "achols/s-a-12.png",
        "achols/s-a-13.png",
        "achols/s-a-14.png",
        "achols/s-a-15.png",
        "achols/s-a-16.png",
      ]
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
        if(this.search === saree.tag){
          sarees.push(saree);
        }
      });
      return sarees;      
    }
  },
  methods:{
    startCustomization(index){
      this.hasSelectedSaree = true;
      this.selectedSaree = index;
      this.selectBackground(this.sarees[index].background);
      this.selectPair(this.sarees[index].pair);
      this.selectAchol(this.sarees[index].achol);
      this.selectGrid(this.sarees[index].grid);
    },
    selectBackground(index){
      this.selectedBackground = index;
      this.background = this.backgrounds[index];
    },
    selectPair(index){
      this.selectedPair = index;
      this.pair = this.pairs[index];
    },
    selectAchol(index){
      this.selectedAchol = index;
      this.achol = this.achols[index];
    },
    selectGrid(index){
      this.selectedGrid = index;
      this.grid = this.grids[index];
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
      if(this.load_more_sarees.length > 0){
        for(let i =0; i< 10; i++){
          this.sarees.push(this.load_more_sarees[i]);
        }
        this.load_more_sarees = [];
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
    }
  }
}
</script>

<style scoped>
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

.parent {
  position: relative;
  top: 0;
  left: 152px;
}
.parent-scale{
  transform: scale(0.75);
}

.image1 {
  position: relative;
  top: 0;
  left: 0;
}
.image2 {
  position: absolute;
  top: 0;
  left: 0;
}

.image2 {
  position: absolute;
  top: 25px;
  left: 0;
}

.image3 {
  position: absolute;
  top: 25px;
  left: 650px;
}

.image4 {
  position: absolute;
  top: 0;
  left: 0;
}

.image5 {
  position: absolute;
  bottom: 0;
  left: 0;
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
  border-left: 40px solid #357DFD;
  position: absolute;
  right: -40px;
  top: 0;
  z-index: 1;
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
  border-left: 40px solid #cfbb81;
  right: -40px;
  z-index: 1;
}
#crumbs ul li a.active-step{
  background-color:#25a94a;
}

#crumbs ul li a.active-step.active-step:after{
  border-left-color:#25a94a;

}


</style>
