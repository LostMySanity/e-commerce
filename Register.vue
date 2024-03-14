<template>
    <div class="flex ">
        <div class="w-1/2  ">
            <carousolrow />
        </div>
        <div class="w-1/2  ">
            <registertab />
        </div>
    </div>
</template>

<script>
export default {
    components: {
        carousolrow: () => import('../CarousolAutoPlay.vue'),
        registertab: () => import('../pages/RegisterTab.vue')
    }
}
</script>