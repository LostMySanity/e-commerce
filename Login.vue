
<template>
  <div>
  <div class="flex ">
    <div class="w-1/2  ">
      <carousolrow />
    </div>
    <div class="w-1/2  ">
      <logintab />
    </div>
  </div>
</div>
</template>

<script>
export default {
  components: {
    carousolrow: () => import('../CarousolAutoPlay.vue'),
    logintab: () => import('../pages/LoginTab.vue')
  }
}
</script>
