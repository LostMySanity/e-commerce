<template>
    <div id="data-list-thumb-view" class="data-list-container">
        <h3>Placed Orders</h3>
        <vs-button icon-pack="feather" icon="icon-arrow-left" to="/partner/product/"></vs-button>
        <partner-single-product-view :viewData="viewModalData" :isViewModalActive="viewModalView"
            @close-view-modal="toggleViewDataModal" />

            <!-- //////////////////// ORDERS VIEW ////////////////// -->
        <div>
            <vx-card>
                <!-- <h2>Orders</h2> --> <vs-table   pagination max-items="10"  :data="placed_orders">

                <template slot="header">
                    <h3>
                        Orders List
                    </h3>
        
                    <vs-spacer></vs-spacer>
                </template>
                <template slot="thead">
                    <vs-th sort-key="orders">
                        Orderid
                    </vs-th>
                    <vs-th sort-key="date">
                        Date
                    </vs-th>
                    <vs-th >
                        Actions
                    </vs-th>
                </template>

                <template slot-scope="{data}">
                    <vs-tr :data="tr" :key="indextr" v-for="(tr, indextr) in data">
                        <vs-td :data="data[indextr].id">
                            {{ data[indextr].id}}
                        </vs-td>

                        <vs-td :data="data[indextr].currentdate ">
                            {{ data[indextr].currentdate}}
                        </vs-td>

                        <vs-td :data="data[indextr].action">
                            <div class="flex gap-2">
                                <!-- {{ data[indextr].}} -->
                                <vs-button :to="{name:'order',params:{id:tr.id} }" class="mr-5 ml-4 round" type="gradient">View Order</vs-button>
                            </div>
                        </vs-td>
                    </vs-tr>
                </template>
            </vs-table>
            </vx-card>
        </div>

       
    </div>
</template>

<script>
import {bus} from '../../main'
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
        viewModalView: false,
        viewModalData: {},
        dataOrder_select: '',
        orderid:null,
        currentdate:'',
        orders:[],
        // status:'accept',
        addNewModalData: false,
        modalData: {},
        /////////////
       placed_orders:[],
       accept_reject_choices:null,
        orderstatus:[],
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
        this.checkUser();
        
        },


    methods: {
        //////////////////select ////
        async selectedOption(event,placedData){
            console.log(placedData,'uiuiuiuoiuoiuiuioiuuiuiouiouiuiouiouioiuuiuio');
            console.log(event,'qwqeqwqewqwqewqewqewqeqewqwqweqewqeqweq');
            console.log(placedData)
                const  updateStatus ={
                    id:placedData.id,
                    productId:placedData.id,
                    partner_id:placedData.partner_id,
                    status:event
               }; console.log(updateStatus)
                  await axios.patch(`http://localhost:3000/placed_order/${updateStatus.id}`,updateStatus).then(
                    res => {
                        if(res){
                            bus.$emit('refresh-placed-order');
                            this.checkUser();
                            // alert('done')
                        }
                    }
                 )
            
            
        },
        /////////selecty////
        async checkUser(){
        const res = await axios.get(`http://localhost:3000/placed_order`)
        this.orders = res.data
        console.log('orders-srders',this.orders);
        this.getProducts(this.orders)
        },

       async getProducts(orders){
        // console.log('orders',orders);
      
        // this.placed_orders=[]
        let val = false
        orders.forEach(element => {
            // console.log('element',element);
            element.orders.forEach(order => {
                // console.log('elelelelel',order.partner_id === Number(this.id));
                if(order.partner_id === Number(this.id)){
                     val = true
                }
            });
            if(val ==true){

                this.placed_orders.push(element)
            }

        });
        console.log('productsarray',this.placed_orders);

        // productsarray
        // for(let i = 0; i < this.products.length; i++){
        // let objectId={
        //     objectid : this.products[i].id
        // }
        //     Object.assign(this.products[i], objectId);
        //  }
        //  console.log('this.products',this.products);
        //  this.placed_orders=[]
        // for(let i = 0; i < productsarray.length; i++){
        //     // console.log('sdfsdfsfwrerwerwer',orders[i].id);
        // this.products.filter((item)=>{  
        // // console.log('sdfsdfsfwrerwerwer',orders[i].productId, 'bmnbmnbmnbnbmnbmn', item);
        //     if(item.id === productsarray[i].productId){    
        //     //    item.id           = orders[i].id,
        //         item.orderId = productsarray[i].id,
        //       item.quantity      = productsarray[i].quantity,
        //       item.priceTemp     = productsarray[i].price,
        //       item.order_status  = productsarray[i].status,
        //     this.placed_orders.push(item)
        //         }
        //       })
        //     }
             console.log('this.placed_orders',this.placed_orders)
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

