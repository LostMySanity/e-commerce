<template>
  <div class="vx-col w-full">
    <vs-button icon-pack="feather" icon="icon-arrow-left" to="/"></vs-button>
    <vx-card>
      <span>Your Orders</span>
      <div class="w-20px" v-for="(items, index) in  orders" :keys="index">
        <item-list-view :items="items">
        </item-list-view>
      </div>
      <vs-divider></vs-divider>
     
    </vx-card>
  </div>
</template>
<script>
import VxTimeline from "@/components/timeline/VxTimeline.vue"
import ListItemView from "../../layouts/components/ecommerce/ListItemView.vue";
import axios from "../../axios";
import {bus} from "../../main"
export default {

    mounted() {
    this.loggedIn = JSON.parse(localStorage.getItem('user-info'))
    // this.userInfo(this.loggedIn);
    this.getproducts();
    this.getPlacedOrder(this.loggedIn);
      bus.$on('refresh-placed-order',() => {
        this.getPlacedOrder(this.loggedIn);
        console.log('busss');
      })
    // this.getPlacedOrder()
    },

  data: () => ({
    myOrders:'',
    placed:'',
    orders:[],
    placedProducts:[],
    ordersPlaced:[],
    
  }),
  components: {
    VxTimeline,
    "item-list-view": ListItemView
  },
  methods:{

      async getproducts(){
        const response  =   await axios.get(`http://localhost:3000/products`)
        this.products   =   response.data

     },

    async getPlacedOrder(logininfo){
           const response  =   await axios.get(`http://localhost:3000/placed_order?userId=${logininfo.id}`)
           const orders   =   response.data

            const orderlist = []
            orders.forEach(order => {
              order.orders.forEach(element => {
                 orderlist.push(element)
              });
            });

            for(let i = 0; i < this.products.length; i++){
            orderlist.filter((product)=>{
              if(product.productId === this.products[i].id){
                product.img         =  this.products[i].img,
                product.name        =  this.products[i].name, 
                product.description =  this.products[i].description,
                product.priceTemp   =  product.price
                this.orders.push(product)
              }
            })
          }
        },
  }
}
</script>


