<template>
  <div class="aspect-video w-full mx-auto max-w-[1500px]">
<!--    Display black screen-->
    <div class="h-full w-full overflow-hidden bg-black rounded relative" v-if="position !== 3">
      <div class="flex gap-1 " :class="positionClass">
        <!-- Display cam feed sources -->
        <div v-for="cam in webcams" :style="{'background-image': 'url(' + cam.path + ')'}"
             class="aspect-video bg-no-repeat bg-cover bg-center rounded m-1" :class="sizeClass"
             :key="cam.id">
        </div>
      </div>

      <div class="flex h-full items-center justify-center ">
        <!-- Display screen share feed -->
        <div v-for="screen in screens" :key="screen.id" :style="{'background-image': 'url(' + screen.path + ')'}"
             class="rounded bg-cover aspect-video h-full"/>
      </div>
    </div>

    <!-- Screen share image is 66% w&h. Webcam image is 33%. Height of both is 80%. -->
    <div class="h-full w-full bg-black flex items-center" v-if="position === 3 && layout === 2">
      <div class="flex  w-full gap-1 h-5/6">
        <!-- Display screen share feed-->
        <div v-for="screen in screens" :key="screen.id" :style="{'background-image': 'url(' + screen.path + ')'}"
             class="rounded bg-cover aspect-video w-2/3 h-5/6"></div>
        <!--Display webcam feed-->
        <div v-for="cam in webcams" :style="{'background-image': 'url(' + cam.path + ')'}"
             class="bg-no-repeat bg-cover bg-center rounded w-1/3 h-5/6" :key="cam.id">

        </div>
      </div>
    </div>

    <!--Bottom buttons bar-->
    <div>
      <!-- WebCam Icons-->
      <div class=" flex flex-cols items-center justify-center my-2" v-if="layout === 1 && webcams.length !== 0">
        <!--  Webcam image is 60% width and height.-->
        <button @click="size = 60">
          <img :src="icons.webcamImgActive" style="height: 55px; width:auto" v-if="size === 60">
          <img :src="icons.webcamImg60" style="height: 55px; width:auto" v-else>
        </button>
        <!-- Webcam image is 80% width and height.-->
        <button @click="size = 80">
          <img :src="icons.webcamImgActive" style="height: 55px; width:auto" v-if="size === 80">
          <img :src="icons.webcamImg80" style="height: 55px; width:auto" v-else>
        </button>
        <!--  Webcam image covers entire stream.-->
        <button @click="size = 100">
          <img :src="icons.webcamImgActive" style="height: 55px; width:auto" v-if="size === 100">
          <img :src="icons.webcamImg80" style="height: 55px; width:auto" v-else>
        </button>
      </div>

      <!--Screenshare Icons-->
      <div class=" flex flex-cols items-center justify-center my-2"
           v-if="layout == 2 && webcams.length > 0 && screens.length > 0">
        <!--      Screen share image is 100% w&h. Webcam image is 25% and is fixed to the right.-->
        <button @click="position = 1">
          <img :src="icons.rightCorner" style="height: 55px; width:auto" v-if="position == 1">
          <img :src="icons.rightCorner" style="height: 55px; width:auto" v-else>
        </button>
        <!-- Screen share image is 100% w&h. Webcam image is 25% and is fixed to the left.-->
        <button @click="position = 2">
          <img :src="icons.leftCorner" style="height: 55px; width:auto" v-if="position == 2">
          <img :src="icons.leftCorner" style="height: 55px; width:auto" v-else>
        </button>
        <!--      Screen share image is 66% w&h. Webcam image is 33%. Height of both is 80%.-->
        <button @click="position = 3">
          <img :src="icons.splitScreen" style="height: 55px; width:auto" v-if="position == 3">
          <img :src="icons.splitScreen" style="height: 55px; width:auto" v-else>
        </button>
      </div>

    </div>

  </div>
</template>

<script>
import webcamImgActive from "@/assets/images/icons/webcam-img.png"
import webcamImg80 from "@/assets/images/icons/Webcam-image-80.png"
import webcamImg60 from "@/assets/images/icons/webcam-image-60.png"

import splitScreen from "@/assets/images/icons/screenshare-66-webcam-33-img.png"
import leftCorner from "@/assets/images/icons/screenshare-img-left-corner-100.png"
import rightCorner from "@/assets/images/icons/screenshare-img-right-corner-100.png"

// Position is used for positioning of screen and webcam on streams screen
// Position 1 -> Screen share image is 100% w&h. Webcam image is 25% and is fixed to the left.
// Position 2 -> Screen share image is 100% w&h. Webcam image is 25% and is fixed to the right.
// Position 3 -> Screen share image is 66% w&h. Webcam image is 33%. Height of both is 80%.

// Size is used for various webcam feed sizes
// Size 100 -> Webcam image covers entire stream.
// Size 80 -> Webcam image is 80% width and height.
// Size 60 -> Webcam image is 60% width and height.

//Layout is used various ui interface to achieve different position and sizes of webcam size and screen

export default {
  data: function () {
    return {
      screens: [],
      webcams: [],
      position: null,
      layout: null,
      size: null,
      icons: {
        webcamImgActive, webcamImg80, webcamImg60, rightCorner, leftCorner, splitScreen
      }
    }
  },

  props: ['streams'],

  watch: {
    streams: {
      handler() {
        this.divideStreams()
      },
      deep: true
    },
  },
  computed: {
    // Position the webcam source
    positionClass() {
      if (this.position === 2) {
        return "absolute bottom-0 left-0 h-32 m-10"
      } else if (this.position === 1) {
        return "absolute bottom-0 right-0 h-32 m-10"
      } else {
        if (this.layout == 1) {
          return 'h-full items-center justify-center'
        } else {
          return "absolute bottom-0 right-0 h-32 m-10"
        }
      }
    },
    // re-size the screenshare source
    sizeClass() {
      if (this.size === 80) {
        return "h-4/5"
      } else if (this.size === 60) {
        return "h-3/5"
      } else {
        return "h-full"
      }
    }
  },

  methods: {
    // Split the webcam and screenshare
    divideStreams() {
      this.screens = [];
      this.webcams = [];
      Object.values(this.streams).forEach((stream) => {
        if (stream.webcam === true) {
          this.webcams.push(stream)
        } else {
          this.screens.push(stream)
        }
      });

      // Reset position and size
      this.size = null
      this.position = null
      if (this.screens.length === 0) {
        this.layout = 1
      } else {
        this.layout = 2
      }
    }
  }

}
</script>

<style>

</style>
