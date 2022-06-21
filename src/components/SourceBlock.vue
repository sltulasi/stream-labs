<template>
  <!-- Stream canvas-->
  <div  :style="{'background-image': 'url(' + source.path + ')'}"
        class="rounded bg bg-cover bg-gray-100 aspect-video w-full flex flex-col">
    <!--add/remove both images to the stream-->
    <div class="flex h-full justify-center">
      <!-- Show Button on stream images-->
      <button @click="this.$emit('addStream')" v-if="!isStreaming"
              class="btn btn-primary text-sm  my-auto" > Show on stream</button>
      <!-- Hide Button on stream images-->
      <button @click="this.$emit('removeStream')" v-if="isStreaming"
              class="btn btn-danger text-sm my-auto" >Hide on stream</button>
    </div>
    <div class="mt-auto rounded p-1 bg-gradient-to-b from-transparent to-black text-gray-100 text-xs w-full">
      {{ source.title }}
    </div>
  </div>

</template>

<script>

export default {
  name: 'SourceTile',
  props: {
    source: Object,
    isStreaming: Object
  }
}
</script>
