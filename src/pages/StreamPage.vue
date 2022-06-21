<template>
  <!-- Stream page -->
  <div class="flex flex-row gap-2 h-screen">
    <div class="column basis-1/4 border-r-2 p-5">
      <div class="flex flex-col gap-3">
        <button class="btn btn-primary" @click.stop="showModal = true">Add Source</button>
        <!--Add Media Source button -->
        <div @click.stop="showModal = true"
             class="rounded-md bg-gray-100 flex justify-center btn-hover h-40 p-3" v-if="sources.length === 0">
          <div class="my-auto text-center   ">
            <div class="text-4xl text-green-700">+</div>
            <b class="text-lg">Add media source</b>
            <p class="text-sm text-gray-500">Screenshare, Camera</p>
          </div>
        </div>
        <!--Display all added sources.-->
        <SourceTile v-for="source in Object.values(sources)" :source="source" @addStream="addStream(source)"
                    @removeStream="removeStream(source)" :isStreaming="streams[source.id]" :key="source.id"/>
      </div>
    </div>

    <div class="w-full h-full flex flex-col justify-between">
      <div class="grow flex items-center m-5 ">
        <!-- Display live streams in 16:9 aspect ratio-->
        <StreamBlock :streams="streams"/>
      </div>
      <!--  Todo: Should convert to component -->
      <div class="flex mx-auto gap-2 p-2 w-1/2 border-2 rounded-t-md">
        <button class="btn-light w-full">Chat</button>
        <button class="btn-light w-full">Record</button>
        <button class="btn-primary w-full">Go live</button>
      </div>
    </div>
  </div>

  <!-- The Modal -->
  <div class="modal block" v-if="showModal">
    <!-- Modal content -->
    <div class="modal-content card shadow-lg w-11/12 md:w-[518px]">
      <div class="p-3">
        <div class="flex item-center">
          <b class="text-bold grow">Add a new media source</b>
          <span class="close" @click="closeModel">&times;</span>
        </div>
        <!--  Screenshare Modal content-->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-3 py-3 h-60">
          <div class="flex btn-hover card bg-gray-100 text-center" :class="sources[screen.id] ? 'box-active' : ''"
               @click="toggleSource(screen)">
            <div class="my-auto">
              <b class="text-md py-3">Screenshare</b>
              <p class="text-sm text-gray-500 mt-3">Share your entire screen, window or a specific Chrome tab</p>
            </div>
          </div>
          <!-- Video Feed Model content-->
          <div class="flex btn-hover card bg-gray-100 text-center" :class="sources[camera.id] ? 'box-active' : ''"
               @click="toggleSource(camera)">
            <div class="my-auto">
              <b class="text-md ">Video Feed</b>
              <p class="text-sm text-gray-500 mt-3">
                Share a feed of your in-built webcam and microphone. If you do not
                have a webcam, you can use a “virtual” webcam such as Streamlabs OBS
                virtual camera
              </p>
            </div>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import SourceTile from "@/components/SourceBlock";
import StreamBlock from "@/components/StreamBlock";
import screenshareImage from "@/assets/images/screenshare.png"
import cameraImage from "@/assets/images/camera.png"


export default {
  data: function () {
    return {
      sources: [],
      screen: {
        id: 1,
        title: 'My screen feed',
        path: screenshareImage
      },
      camera: {
        id: 2,
        title: 'My video feed',
        path: cameraImage,
        webcam: true
      },

      streams: {},
      showModal: false
    }
  },

  props: {
    msg: String
  },
  components: {SourceTile, StreamBlock},
  methods: {
    // Add or remove sources toggle
    toggleSource(source) {
      if (this.sources[source.id]) {
        delete this.source[source.id]
      } else {
        this.sources[source.id] = source
      }
    },
    // Add screen share image
    addStream(stream) {
      this.streams[stream.id] = stream
    },
    // Remove screen share image
    removeStream(stream) {
      delete this.streams[stream.id]
    },
    // Close the Model
    closeModel() {
      this.showModal = false
    }
  }
}
</script>

<style scoped>
.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(255, 255, 255);
  background-color: rgba(248, 248, 248, 0.6);
}

/* Modal Content/Box */
.modal-content {
  background-color: #fefefe;
  margin: 15% auto;
}

/* The Close Button */
.close {
  color: #aaa;
  font-size: 20px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}


</style>
