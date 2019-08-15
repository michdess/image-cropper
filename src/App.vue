<template>
  <div id="app" class="flex flex-col items-center justify-center h-screen w-full bg-gray-200">
    <h1 class="text-3xl sm:text-4xl md:text-4xl xl:text-5xl leading-tight font-semibold text-gray-800">Image Cropper</h1>
    <p class="text-3xl sm:text-4xl md:text-4xl xl:text-5xl leading-tight font-semibold text-indigo-500">Get started</p>
    
    <div v-if="!img" class="my-6 relative overflow-hidden">
      <button class="px-5 py-3 rounded-lg uppercase text-lg tracking-wider text-white bg-indigo-500 shadow-lg inline-block m-auto shadow-lg focus:outline-none focus:shadow-outline">Select File</button>
      <input class="cursor-pointer absolute top-0 left-0 bottom-0 right-0 opacity-0" type="file" id="imageLoader" ref="imageUp" @change="handleImage($event)"/>
    </div>
    <div v-else class="my-6 relative overflow-hidden">
      <button class="px-5 py-3 rounded-lg uppercase text-lg tracking-wider text-white bg-red-500 shadow-lg inline-block m-auto shadow-lg focus:outline-none focus:shadow-outline" @click="reset()">Reset</button>
    </div>

    <div class="flex justify-around w-full px-6">
      <div class="flex flex-col">
        <p class="text-xl text-gray-800 m-auto">Original</p>
        <canvas id="originalUpload" class="border border-gray-900" 
          :height="height" 
          :width="width" 
          @mousedown="selectStart" 
          @mouseup="selectEnd" 
          @mousemove="selecting">
        </canvas>
      </div>
      <div class="flex flex-col">
        <p class="text-xl text-gray-800 m-auto">Selection</p>
        <canvas id="selectedArea" :height="height" :width="width" class="border border-gray-900"></canvas>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      height: 0,
      width: 0,
      img: null,
      rect: {},
      beingDragged: false,
      canvas: null,
      ctx: null,
      selectionCanvas: null,
      selectedContext: null,
    }
  },
  methods: {
    selectStart(event){
      //Click detected - process and handle a selection of the image
      this.rect.startX = event.clientX - this.canvas.offsetLeft;
      this.rect.startY = event.clientY - this.canvas.offsetTop;
      this.beingDragged = true;
    },
    selectEnd(){
      //Selection finished. Clear the rectangle and show the selection in the selected canvas.
      this.beingDragged = false;
      this.ctx.beginPath();
      this.ctx.clearRect(0,0,this.canvas.width,this.canvas.height);
      if(this.img != null){
          this.ctx.drawImage(this.img,0,0, this.img.width, this.img.height, 0,0, this.img.width * (this.width/this.img.width), this.img.height * (this.height/this.img.height));
      }
      this.showSelection();
    },
    selecting(event){
      //handling the mouse movement during selection
      if (this.beingDragged) {
          this.rect.w = (event.clientX - this.canvas.offsetLeft) - this.rect.startX;
          this.rect.h = (event.clientY - this.canvas.offsetTop) - this.rect.startY;
          this.ctx.clearRect(0,0,this.canvas.width,this.canvas.height);
          this.draw();
      }
    },
    draw() {
      //Draw the rectangle outline that the user is selecting. 
      //Also ensure the background image remains visible
      //as the selection box is redrawn with movement.
      this.ctx.beginPath();
      this.ctx.fillStyle = "white";
      this.ctx.rect(this.rect.startX, this.rect.startY, this.rect.w, this.rect.h);
      if(this.img != null){
          this.ctx.drawImage(this.img,0,0, this.img.width, this.img.height, 0,0, this.img.width * (this.width/this.img.width), this.img.height * (this.height/this.img.height));
      }
      this.ctx.stroke();
    },
    showSelection(){
      //as you drag across the image show the user the square they are selecting
      let self = this
      let img = new Image();
      img.onload = function() {
        self.selectedContext.drawImage(this, 
          (self.rect.startX * (this.width/self.width)),
          self.rect.startY * (this.height/self.height), self.rect.w * (this.width/self.width), self.rect.h * (this.height/self.height), 
          0, 0, self.width, self.height);
      }
      img.src = this.img.src;
    },
    handleResize() {
        // Calculate new canvas size based on window (0.45 gives the canvases a little breathing room.)
        this.height = window.innerHeight * 0.45;
        this.width = window.innerWidth * 0.45;
        //if there is an image need to redraw it.
        this.$nextTick(() => {
          if(this.img){
            this.ctx.drawImage(this.img,0,0, this.img.width, this.img.height, 0,0, this.img.width * (this.width/this.img.width), this.img.height * (this.height/this.img.height));
          }
        })
    },
    handleImage(e){
      //Take the selected image and add it to the canvas + set vars so we can manipulate it later.
      let reader = new FileReader();
      let self = this;
      reader.onload = function(event){
          self.img = new Image();
          self.img.onload = function(){
            //Draw the image - I am taking into account that the canvas size may not match the image and scaling appropriately
            self.ctx.drawImage(self.img,0,0, self.img.width, self.img.height, 0,0, self.img.width * (self.width/self.img.width), self.img.height * (self.height/self.img.height));
          }
          //save the image as a data attr.
          self.img.src = event.target.result;
      }
      reader.readAsDataURL(e.target.files[0]);   
    },
    reset(){
      //Reset the image cropper so users can try a different image.
      this.img = null;
      this.ctx.clearRect(0,0,this.canvas.width,this.canvas.height);
      this.selectedContext.clearRect(0,0,this.canvas.width,this.canvas.height);
    }
  },
  mounted() {
    //Initialise the canvases and contexts we want
    this.canvas = document.getElementById('originalUpload');
    this.ctx = this.canvas.getContext('2d');
    this.selectionCanvas = document.getElementById('selectedArea');
    this.selectedContext = this.selectionCanvas.getContext('2d');
    //Watch for resizing of the window - let's keep this thing responsive.
    window.addEventListener('resize', this.handleResize);
    this.handleResize();
  },
}
</script>

<style src="./assets/css/tailwind.css"></style>

