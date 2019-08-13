<template>
  <div id="app" class="flex flex-col items-center justify-center h-screen w-full bg-gray-200">
    <h1 class="text-3xl sm:text-4xl md:text-4xl xl:text-5xl leading-tight font-semibold text-gray-800">Image Cropper</h1>
    <p class="text-3xl sm:text-4xl md:text-4xl xl:text-5xl leading-tight font-semibold text-indigo-500">Get started</p>
    <button class="my-6 px-5 py-3 rounded-lg uppercase text-lg tracking-wider text-white bg-indigo-500 shadow-lg inline-block m-auto focus:outline-none focus:shadow-outline" @click="updateCanvas()">Select File</button>
    <canvas id="imageUpload" class="border border-gray-900"></canvas>
  </div>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
      imageSelected: false,
    }
  },
  components: {

  },
  methods: {
    updateCanvas: function (){
      let canvas = document.getElementById('imageUpload');
      let ctx = canvas.getContext('2d');
          ctx.clearRect(0,0,canvas.width,canvas.height);
          ctx.fillStyle = "black";
          ctx.font="20px Georgia";
          ctx.fillText("this.exampleContent",10,50);
          ctx.canvas.width  = window.innerWidth * 0.5;
          ctx.canvas.height = window.innerHeight * 0.5;
    }
  },
}
</script>

<style src="./assets/css/tailwind.css"></style>
