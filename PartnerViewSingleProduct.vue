<template>
    <div >
        <vs-popup classContent="popup" title="Product" :active.sync="isViewModalActiveLocal">

            <div>
                <vx-card>
                    <div class="item-content">

                        <!-- Item Main Info -->
                        <div class="product-details p-6">
                            <div class="vx-row mt-6">
                                <div class="vx-col md:w-2/5 w-full flex items-center justify-center">
                                    <div class="product-img-container w-3/5 mx-auto mb-10 md:mb-0">

                                        <img :src="dataImg" :alt="dataName" class="responsive">
                                    </div>
                                </div>

                                <!-- Item Content -->
                                <div class="vx-col md:w-3/5 w-full">

                                    <h3>{{ dataName }}</h3>

                                    <p class="my-2">
                                        <span class="mr-2">by</span>
                                        <span>{{ dataBrand }}</span>
                                    </p>
                                    <p class="flex items-center flex-wrap">
                                        <span class="text-2xl leading-none font-medium text-primary mr-4 mt-2">${{
                                           dataPrice}}</span>
                                        <span
                                            class="pl-4 mr-2 mt-2 border border-solid d-theme-border-grey-light border-t-0 border-b-0 border-r-0"></span>
                                        <span class="cursor-pointer leading-none mt-2">424 ratings</span>
                                    </p>

                                    <vs-divider />

                                    <p>{{ dataDesc }}</p>

                                    <vs-list class="product-sales-meta-list px-0 pt-4">
                                        <vs-list-item v-if="dataFree_shipping" class="p-0 border-none"
                                            title="Free Sheeping" icon-pack="feather" icon="icon-truck" />
                                        <vs-list-item class="p-0 border-none" title="EMI options available"
                                            icon-pack="feather" icon="icon-dollar-sign"></vs-list-item>
                                    </vs-list>

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
                                    <!-- /Quantity -->

                                    <div class="vx-row">
                                        <div class="vx-col flex flex-wrap items-center">
                                            <vs-button class="mr-4" type="border" icon-pack="feather" color="#1551b1"
                                                icon="icon-facebook" radius></vs-button>
                                            <vs-button class="mr-4" type="border" icon-pack="feather" color="#00aaff"
                                                icon="icon-twitter" radius></vs-button>
                                            <vs-button class="mr-4" type="border" icon-pack="feather" color="#c4302b"
                                                icon="icon-youtube" radius></vs-button>
                                            <vs-button class="mr-4" type="border" icon-pack="feather" color="#405DE6"
                                                icon="icon-instagram" radius></vs-button>
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

    data:()=>({
        dataId: null,
        dataName: "",
        dataCategory: null,
        dataSubCategory: null,
        dataImg: null,
        dataOrder_status: "pending",
        dataPrice: 0,
        dataDesc: "",
        dataQuan: '',
        dataFree_shipping:'',
        dataBrand:''
    }),

    props: {
        isViewModalActive: {
            type: Boolean
        },
        viewData: {
            type: Object,
            default:()=>{}
        }

    },
    computed:{
        isViewModalActiveLocal:{
            get(){
              return  this.isViewModalActive
            },
            set(val){
                if(!val){
                    return this.$emit('close-view-modal')
                }
            }
        }
    },

    watch: {
        isViewModalActive(val){
            if(val){
                console.log(this.viewData);
                let { subcategory, category, id, img, name, order_status, price, desc, quantity } = JSON.parse(JSON.stringify(this.viewData))
                this.dataId = id
                this.dataCategory = category
                this.dataSubCategory = subcategory
                this.dataImg = img
                this.dataName = name
                this.dataOrder_status = order_status
                this.dataPrice = price
                this.dataDesc = desc
                this.dataQuan = quantity

            }
        }
    }
}
</script>