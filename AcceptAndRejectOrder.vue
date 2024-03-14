<template>
    <div>
        <vs-button icon-pack="feather" icon="icon-arrow-left" to=""></vs-button>
         <vs-table ref="table" pagination max-items="5"  :data="orders">
            <div slot="header" class="flex flex-wrap-reverse items-center flex-grow justify-between">
                <div class="flex flex-wrap-reverse items-center">
                </div>
            </div>

            <template slot="thead">
                <vs-th>Image</vs-th>
                <vs-th sort-key="name">Name</vs-th>
                <vs-th sort-key="quantity">Quantity</vs-th>
                <!-- <vs-th sort-key="popularity">Popularity</vs-th> -->
                <vs-th sort-key="order_status">Order Status</vs-th>
                <vs-th>Orders</vs-th>
                <vs-th sort-key="price">Price</vs-th>
                <vs-th>Action</vs-th>
            </template>

            <template slot-scope="{data}">
                <tbody>
                    <vs-tr :data="tr" :key="indextr" v-for="(tr, indextr) in orders">

                        <vs-td class="img-container">
                            <img :src="tr.img" class="product-img" />
                        </vs-td>

                        <vs-td>
                            <p class="product-name font-medium truncate">{{ tr.name }}</p>
                        </vs-td>

                        <vs-td>
                            <p class="product-quantity">{{ tr.quantity }}</p>
                        </vs-td>

                        <!-- <vs-td>
                            <vs-progress :percent="Number(tr.popularity)" :color="getPopularityColor(Number(tr.popularity))"
                                class="shadow-md" /><span>{{ tr.popularity }}%</span>
                        </vs-td> -->

                        <vs-td>
                            <vs-chip :color="getOrderStatusColor(tr.order_status)" class="product-order-status">{{
                                tr.order_status }}</vs-chip>
                        </vs-td>

                        <vs-td>
                            <v-select @input="acceptOrReject($event,tr,indextr)" v-model="tr.order_status" :options="['accept', 'reject']"></v-select>
                            <!-- <select-accept-reject @accept_reject_choices="updateSelection($event)" :product="tr" /> -->
                            <!-- <vs-select @input="selectedOption(tr)"  label="Orders" v-model="tr.value">
                             <vs-select-item class="text-bold" :key="index" :value="item.value" :text="item.text"
                            v-for="item, index in order_select_choices" />
                            </vs-select> -->
                        </vs-td>

                        <vs-td>
                            <p class="product-price">${{ tr.priceTemp }}</p>
                        </vs-td>

                        <vs-td class="whitespace-no-wrap">
                            <feather-icon icon="EyeIcon" svgClasses="w-5 h-5 hover:text-primary stroke-current"
                                @click.stop="viewProductData(tr)" />
                            <!-- <feather-icon icon="TrashIcon" svgClasses="w-5 h-5 hover:text-danger stroke-current"
                                class="ml-2" @click.stop="deleteData(tr.id)" /> -->
                        </vs-td>
                    </vs-tr>
                </tbody>
            </template>
        </vs-table>
    </div>
</template>

<script>
import vSelect from 'vue-select';
import axios from 'axios'
export default {
   props:{
    id:{
        type:[Number,String]
    }
   },
    data() {
        return {
        productData: [],
        loggedIn:{},
        viewModalView: false,
        viewModalData: {},
        dataOrder_select: '',
        orderid:null,
        currentdate:'',
        products:[],
        orders:[],
        // status:'accept',
        addNewModalData: false,
        modalData: {},
        /////////////
       placed_orders:[],
       accept_reject_choices:null,
        // orderstatus:[],
        order_select_choices: [
            {
                id: 0,
                text: "Accept",
                value: 1
            },
            {
                id: 1,
                text: "Reject",
                value: 2
            }
        ],
        }
    },
    components: {
       
        "partner-single-product-view": () => import('./PartnerViewSingleProduct.vue'),
        "select-accept-reject": () => import('../../layouts/components/PartnerPlaceOrderSelect.vue'),
         vSelect
    },

   
        mounted(){
            this.loggedIn = JSON.parse(localStorage.getItem('user-info'));
            this.checkUser(this.loggedIn);
                this.getProducts();
         
        },


    methods: {
        //////////////////select ////
        async acceptOrReject(event,placedData,index){
            console.log(placedData,'uiuiuiuoiuoiuiuioiuuiuiouiouiuiouiouioiuuiuio');
            console.log(index,'qwqeqwqewqwqewqewqewqeqewqwqweqewqeqweq');
            console.log(placedData)

            //     const  updateStatus ={
            //         id:placedData.id,
            //         productId:placedData.id,
            //         partner_id:placedData.partner_id,
            //         orderstatus:event
            //    }; 
            let updateStatus={};
               console.log(updateStatus)
               const mappedarray =[]
               let obj ={}
               updateStatus ={    
                 partner_id  : placedData.partner_id,
                 userId      : placedData.userId,
                 productId   : placedData.productId,
                 objectId    : placedData.objectId,
                 quantity    : placedData.quantity,
                 price       : placedData.price,
                 order_status: event
                 }
               mappedarray.push(  
                updateStatus)
                    
               obj={    orders:mappedarray,
                            // currentdate:date,
                            userId: placedData.userId
                        }
                        console.log('fjkhjkfhjkdfhjkdfshjkdfsjkhdfsjkhfdsjkhfdsjkhdfjksdfjks',obj);
            
            //    const response =   await axios.patch(`http://localhost:3000/placed_order/${this.id}`,obj).then(
            //         res => {
            //             if(res){
            //                 alert('done')
            //                 bus.$emit('refresh-placed-order');
            //                 this.checkUser();
            //             }
            //         }
            //      )
            // console.log('response',response);
            
        },
        /////////selecty////
         async checkUser(logininfo){

        console.log('logininfo',logininfo)
        // this.getProducts(this.orders)
        const response  =   await axios.get(`http://localhost:3000/products?partner_id=${logininfo.id}`)
        this.products   =   response.data
        console.log('orders-srders',this.products );
     
     },

       async getProducts(){
           const response  =   await axios.get(`http://localhost:3000/placed_order?id=${this.id}`)
           const orders   =   response.data
           console.log('ordersmdfshjkfdhsjk',    orders );
           const orderData = orders[0].orders
           orderData.forEach(order => {
                 if(order.partner_id === this.loggedIn.id){
                    this.placed_orders.push(order)
                 }
            });

            for(let i = 0; i < this.products.length; i++){
            this.placed_orders.filter((product)=> {
               console.log('ogdssdfsjk',product.productId === this.products[i].id);
                if(product.productId === this.products[i].id){
                     product.img      = this.products[i].img,
                    product.name      = this.products[i].name,  
                    product.priceTemp = product.price
                    this.orders.push(product)
                }
            })
        }
        console.log('this.placed_orders',this.orders)
   
    
        },

        addNewData() {
            this.modalData = {}
            this.toggleDataModal(true)
        },


        editData(data) {
            this.modalData = data
            this.toggleDataModal(true)
        },
        getOrderStatusColor(status) {
            if (status == 'pending') return "warning"
            if (status == 'accept') return "success"
            if (status == 'reject') return "danger"
            if (status ==  null) return "warning"
            return "primary"
        },
        // getPopularityColor(num) {
        //     if (num > 90) return "success"
        //     if (num > 70) return "primary"
        //     if (num >= 50) return "warning"
        //     if (num < 50) return "danger"
        //     return "primary"
        // },
        toggleDataModal(val = false) {
            console.log(val, 'value');
            this.addNewModalData = val
        },
        viewProductData(data) {
            console.log(data);
            this.viewModalData = data
            this.toggleViewDataModal(true)
        },
        toggleViewDataModal(val = false) {
            this.viewModalView = val
        },
    }

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
