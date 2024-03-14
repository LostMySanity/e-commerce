<template>
    <div>
        <vs-popup classContent="popup" title="Product" :active.sync="popupActive">
            <div>
                <vx-card>
                    <div class="item-content">
                        <!-- Item Main Info -->
                        <div class="product-details p-6">
                            <div class="vx-row mt-6">
                                <div class="vx-col md:w-2/5 w-full flex items-center justify-center">
                                    <div class="product-img-container w-3/5 mx-auto mb-10 md:mb-0">
                                        <img :src="proData.img" :alt="proData.name" class="responsive">
                                    </div>
                                </div>
                                <!-- Item Content -->
                                <div class="vx-col md:w-3/5 w-full">

                                    <h3>{{ proData.name }}</h3>

                                    <p class="my-2">
                                        <span class="mr-2">by</span>
                                        <span>{{ proData.brand }}</span>
                                    </p>
                                    <p class="flex items-center flex-wrap">
                                        <span class="text-2xl leading-none font-medium text-primary mr-4 mt-2">${{
                                            proData.price }}</span>
                                        <span
                                            class="pl-4 mr-2 mt-2 border border-solid d-theme-border-grey-light border-t-0 border-b-0 border-r-0"></span>
                                        <span class="cursor-pointer leading-none mt-2">424 ratings</span>
                                    </p>
                                    <vs-divider />
                                    <p>{{ proData.description }}</p>
                                    <vs-list class="product-sales-meta-list px-0 pt-4">
                                        <vs-list-item v-if="proData.free_shipping" class="p-0 border-none"
                                            title="Free Sheeping" icon-pack="feather" icon="icon-truck" />
                                        <vs-list-item class="p-0 border-none" title="EMI options available"
                                            icon-pack="feather" icon="icon-dollar-sign"></vs-list-item>
                                    </vs-list>
                                    <vs-divider />
                                    <vs-divider />
                                    <!-- Quantity -->
                                    <div class="vx-row">
                                        <div class="vx-col w-full">
                                            <p class="my-2">
                                                <span>Available</span>
                                                <span class="mx-2">-</span>
                                                <span class="text-success">In Stock</span>
                                            </p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </vx-card>
            </div>
        </vs-popup>
    </div>
</template>

<script>

export default {
    data:() =>  ({
        popupActive:false
    }),

    props: {
        popup: {
            type: Boolean
        },

        proData: {
            type: [String,Object]
        }

    },
        mounted(){
            this.popupActive = this.popup
        },
        watch: {
            popupActive(val) {
            this.$emit('close-modal', val)
        }
    }
    
}
</script>