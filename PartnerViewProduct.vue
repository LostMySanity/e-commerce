<template>
    <div id="data-list-thumb-view" class="data-list-container">

        <vs-button v-if="roletype == 'admin'" class="mb-4" icon-pack="feather" to="/admin/partner" icon="icon-arrow-left"></vs-button>
        <!-- /////////////////// EDIT AND ADD PRODUCT MODAL ///////////////// -->
        
        <template v-if="roletype == 'partner'">
        <add-edit-category-and-product :isAdd="isAdd" :modalData="modalData" @refresh="refresh($event)" @close-modal="toggleDataModal"
            :isModalActive="isModalActive" v-if="isModalActive" />
        </template>

        <!-- /////////////////// VIEW SINGLE PRODUCT COMPONENT ///////////////// -->  
        <partner-single-product-view :viewData="viewModalData" :isViewModalActive="viewModalView"
            @close-view-modal="toggleViewDataModal" />

        <!-- /////////////////// TABLE ///////////////// --> 
        <vs-table   pagination max-items="10"  :data="productList">
            <template v-if="roletype == 'partner'">
            <div slot="header" class="flex flex-wrap-reverse items-center flex-grow justify-between">

                <div class="flex flex-wrap-reverse items-center">
                    <!-- ADD NEW -->
                    <div class="p-3 mb-4 mr-4 rounded-lg cursor-pointer flex items-center justify-between text-lg font-medium text-base text-primary border border-solid border-primary"
                        @click="addNewData">
                        <feather-icon icon="PlusIcon" svgClasses="h-4 w-4" />
                        <span class="ml-2 text-base text-primary">Add New</span>
                    </div>
                        <!-- View Placed NEW -->
                        <div class="p-3 mb-4 mr-4 rounded-lg cursor-pointer flex items-center justify-between text-lg font-medium text-base text-primary border border-solid border-primary"
                        @click="ViewPlaced(true)">
                        <feather-icon icon="EyeIcon" svgClasses="h-4 w-4" />
                        <span class="ml-2 text-base text-primary">Placed Orders</span>
                    </div>
                </div>

            </div>
        </template>

            <template slot="thead">
                <vs-th>Image</vs-th>
                <vs-th sort-key="name">Name</vs-th>
                <!-- <vs-th sort-key="category">Category</vs-th> -->
                <!-- <vs-th  v-if="roletype == 'partner'" sort-key="popularity">Popularity</vs-th> -->
                <vs-th  sort-key="price">Price</vs-th>
                <vs-th>Action</vs-th>
            </template>

            <template slot-scope="{data}">
                <tbody>
                    <vs-tr :data="tr" :key="indextr" v-for="(tr, indextr) in productList">
                        <vs-td class="img-container">
                            <img :src="tr.img" class="product-img" />
                        </vs-td>

                        <vs-td>
                            <p class="product-name font-medium truncate">{{ tr.name }}</p>
                        </vs-td>
                    <!-- 
                        <vs-td>
                            <p class="product-category">{{ tr.category }}</p>
                        </vs-td> -->
<!-- 
                        <vs-td v-if="roletype == 'partner'">
                            <vs-progress  :percent="Number(tr.popularity)" :color="getPopularityColor(Number(tr.popularity))"
                                class="shadow-md" /><span>{{ tr.popularity}}%</span>
                        </vs-td> -->
                        <vs-td>
                            <p class="product-price">${{ tr.price }}</p>
                        </vs-td>
                        <vs-td class="whitespace-no-wrap">
                            <feather-icon icon="EyeIcon" svgClasses="w-5 h-5 hover:text-primary stroke-current"
                                @click.stop="viewProductData(tr)" />
                            <template v-if="roletype == 'partner'">
                            <feather-icon icon="EditIcon" class="ml-2"
                                svgClasses="w-5 h-5 hover:text-primary stroke-current" @click.stop="editData(tr)" />
                            <feather-icon icon="TrashIcon" svgClasses="w-5 h-5 hover:text-danger stroke-current"
                                class="ml-2" @click.stop="deleteProduct(tr.id)" />
                            </template>
                            </vs-td>
                    </vs-tr>
                </tbody>
            </template>
        </vs-table>
    </div>
</template>
  
<script>

import axios from '../../axios';

export default {
    props: {
    id: {
      type: [Number, String]
    }
  },
    data() {
        return {
            partner_id:'',
            roletype:'',
            deleteProductId:null,
            partnerViewPlacedOrder:false,
            selected: [],
            currentProductCount:null,
            viewModalView: false,
            viewModalData: {},
            order_select_choices:[
                {
                    text:"accept",
                    value:"true"
                },
                {
                    text:"Reject",
                    value:"false"
                }
            ],
            dataOrder_select:'',
            isModalActive: false,
            modalData: {},
            productList:''
        }
    },
    components: {
        "add-edit-category-and-product": () => import('./PartnerAddAndEditProduct.vue'),
        "partner-single-product-view": () => import('./PartnerViewSingleProduct.vue')
    },
    async mounted() {
       const isloggedIn  =   JSON.parse(localStorage.getItem('user-info'))
       this.roletype     =  isloggedIn.role 
       this.partner_id   =  isloggedIn.id 
       console.log(this.roletype,'isloggedIn');
        this.refresh();
        
        },

    
    methods: {
       async refresh(){
        if(this.roletype == 'partner'){
            let res = await axios.get(`http://localhost:3000/products?partner_id=${this.partner_id}`)
            this.productList = res.data
            this.currentProductCount = this.productList.length
            console.log(this.currentProductCount,"res");
            this.updatePartnerProductCount();
        }else{
            axios.get(`http://localhost:3000/products?partner_id=${this.id}`).then(res => this.productList = res.data)
        }
    },

        async updatePartnerProductCount(){
            const productCount={
                product: this.currentProductCount
            }
        await axios.patch(`http://localhost:3000/login/${this.partner_id}`,productCount )

    },
      
        ViewPlaced(){
            this.$router.push(
                {name:'partner-placed-order-view',
                params:{id:this.partner_id}}
            );
        
        },

        //////////////////  DELETE SECTION //////////////////////
        deleteProduct(deleteId) {
            this.deleteProductId   =  deleteId
            this.$vs.dialog({
                type    : 'confirm',
                color   : 'danger',
                text    : 'Confirm Delete this Product',
                accept  : this.acceptAlert
            })
        },

      async acceptAlert() {
            // this.finalDelete(this.deleteProductId)
            await axios.delete(`http://localhost:3000/products/${this.deleteProductId}`).then(res =>{
                if(res){
                    this.$vs.notify({
                    color    : 'danger',
                    title    : 'Delete',
                    text     : 'The selected Product was successfully deleted',
                    position : 'top-right'
                })
                this.refresh()
               } }).catch(error => {
                this.$vs.notify({
                    color    : 'danger',
                    title    : 'Error',
                    text     : 'unable to delete product',
                    position : 'top-right'
                })
                })
        },

            ///////////////// ADD AND EDIT SECTION //////////////////////
        addNewData() {
            this.modalData = {};
            this.toggleDataModal(true);
            this.isAdd    = true
        },

        editData(data) {
            this.modalData = data
            this.toggleDataModal(true)
            this.isAdd    = false
        },
        
        toggleDataModal(val = false) {
            console.log(val, 'value');
            this.isModalActive = val
        },
        
        getOrderStatusColor(status) {
            if (status == 'on_hold') return "warning"
            if (status == 'delivered') return "success"
            if (status == 'canceled') return "danger"
            return "primary"
        },
        getPopularityColor(num) {
            if (num > 90) return "success"
            if (num > 70) return "primary"
            if (num >= 50) return "warning"
            if (num < 50) return "danger"
            return "primary"
        },
        viewProductData(data) {
            console.log(data);
            this.viewModalData = data
            this.toggleViewDataModal(true)
        },
        toggleViewDataModal(val = false) {
            this.viewModalView = val
        },
    },
}
</script>
  
<style lang="scss">
#data-list-thumb-view {
    .vs-con-table {

        .product-name {
            max-width: 23rem;
        }

        .vs-table--header {
            display: flex;
            flex-wrap: wrap-reverse;
            margin-left: 1.5rem;
            margin-right: 1.5rem;

            >span {
                display: flex;
                flex-grow: 1;
            }

            .vs-table--search {
                padding-top: 0;

                .vs-table--search-input {
                    padding: 0.9rem 2.5rem;
                    font-size: 1rem;

                    &+i {
                        left: 1rem;
                    }

                    &:focus+i {
                        left: 1rem;
                    }
                }
            }
        }

        .vs-table {
            border-collapse: separate;
            border-spacing: 0 1.3rem;
            padding: 0 1rem;


            tr {
                box-shadow: 0 4px 20px 0 rgba(0, 0, 0, .05);

                td {
                    padding: 10px;

                    &:first-child {
                        border-top-left-radius: .5rem;
                        border-bottom-left-radius: .5rem;
                    }

                    &:last-child {
                        border-top-right-radius: .5rem;
                        border-bottom-right-radius: .5rem;
                    }

                    &.img-container {
                        // width: 1rem;
                        // background: #fff;

                        span {
                            display: flex;
                            justify-content: flex-start;
                        }

                        .product-img {
                            height: 110px;
                        }
                    }
                }

                td.td-check {
                    padding: 20px !important;
                }
            }
        }

        .vs-table--thead {
            th {
                padding-top: 0;
                padding-bottom: 0;

                .vs-table-text {
                    text-transform: uppercase;
                    font-weight: 600;
                }
            }

            th.td-check {
                padding: 0 15px !important;
            }

            tr {
                background: none;
                box-shadow: none;
            }
        }

        .vs-table--pagination {
            justify-content: center;
        }
    }
}
</style>
  