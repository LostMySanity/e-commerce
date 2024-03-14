<template>
    <div>
        <vs-button class="mb-4" icon-pack="feather" to="/" icon="icon-arrow-left"></vs-button>
      
        <vx-card>
            <div class="item-content">
                <!-- Item Main Info -->
                <div v-for="(product, index) in productDetails" :key="index" class="product-details p-6">
                    <div class="vx-row mt-6">
                        <div class="vx-col md:w-2/5 w-full flex items-center justify-center">
                            <div class="product-img-container w-3/5 mx-auto mb-10 md:mb-0">
                                <!-- <img :src="product.img" class="" responsive> -->
                                <multiple-image-view :items="product.img" />
                            </div>
                        </div>
                        <div class="vx-col">
                            <!-- item content -->
                            <div class="vx-col md:w-3/5 w-full">

                                <h3>{{ product.name }}</h3>

                                <p class="my-2">
                                    <span class="mr-2">by</span>
                                    <span>{{ product.brand }}</span>
                                </p>
                                <p class="flex items-center flex-wrap">
                                    <span class="text-2xl leading-none font-medium text-primary mr-4 mt-2">${{
                                        product.price }}</span>
                                    <span
                                        class="pl-4 mr-2 mt-2 border border-solid d-theme-border-grey-light border-t-0 border-b-0 border-r-0"><star-rating
                                            :show-rating="false" :rating="product.rating" :star-size="20"
                                            read-only /></span>
                                    <span class="cursor-pointer leading-none mt-2">rating</span>
                                </p>

                                <vs-divider></vs-divider>
                                <p>
                                    <span class="cursor-pointer leading-none mt-2">{{ product.desc }}</span>
                                </p>
                                <vs-list class="product-sales-meta-list px-0 pt-4">
                                    <vs-list-item v-if="product.free_shipping" class="p-0 border-none" title="Free Sheeping"
                                        icon-pack="feather" icon="icon-truck" />
                                    <vs-list-item class="p-0 border-none" title="EMI options available" icon-pack="feather"
                                        icon="icon-dollar-sign"></vs-list-item>
                                </vs-list>
                                <vs-divider></vs-divider>
                                <div class="vx-row">
                                    <div class="vx-col w-full">
                                        <span v-if="product.quantity >= 1">Available</span>
                                        <span v-if="product.quantity >= 1" class="text-success">in Stock</span>
                                        <span v-else class="text-Danger">Out of Stock</span>
                                        <div>
                                            <span v-if="product.quantity <= 5 && product.quantity >= 1"
                                                class="text-danger">Hurry Only Few Left {{ product.inventory }}</span>
                                        </div>
                                        <div class="flex w-full">
                                            <vs-button @click="toggleCartProduct(product)" v-if="!checkIfInCart(product.id)" class=""
                                                icon-pack="feather" icon="icon-shopping-cart">
                                                ADD TO CART
                                            </vs-button>
                                            <vs-button @click="toggleCartProduct(product)" v-else class="" icon-pack="feather"
                                                icon="icon-shopping-cart">
                                                VIEW IN CART
                                            </vs-button>
                                            <vs-button @click="wishlistButtonToggle(product.id, index)" v-if="chekcIfinWishlist(product.id)" key="filled"
                                                class="ml-2 Danger" icon-pack="feather" icon="icon-heart" color="danger">
                                                WISHLIST
                                            </vs-button>
                                            <vs-button @click="wishlistButtonToggle(product.id,index)" v-else key="border" type="border"
                                                class="ml-2 Danger" icon-pack="feather" icon="icon-heart" color="danger">
                                                WISHLIST
                                            </vs-button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>

                    </div>
                    <div class="py-24 mb-16 mt-10 text-center item-features">
                        <div class="vx-row">
                            <div class="vx-col md:w-1/3 w-full">
                                <div class="w-64 mx-auto mb-16 md:mb-0">
                                    <feather-icon icon="AwardIcon" svgClasses="h-12 w-12 text-primary stroke-current"
                                        class="block mb-4" />
                                    <span class="font-semibold text-lg">100% Original</span>
                                    <p class="mt-2">original </p>
                                </div>
                            </div>
                            <div class="vx-col md:w-1/3 w-full">
                                <div class="w-64 mx-auto mb-16 md:mb-0">
                                    <feather-icon icon="ClockIcon" svgClasses="h-12 w-12 text-primary stroke-current"
                                        class="block mb-4" />
                                    <span class="font-semibold text-lg">10 Day Replacement</span>
                                    <p class="mt-2">It falls it breaks simple</p>
                                </div>
                            </div>
                            <div class="vx-col md:w-1/3 w-full">
                                <div class="w-64 mx-auto">
                                    <feather-icon icon="ShieldIcon" svgClasses="h-12 w-12 text-primary stroke-current"
                                        class="block mb-4" />
                                    <span class="font-semibold text-lg">1 Year Warranty</span>
                                    <p class="mt-2">Yeah! one year Warranty Hopefully.</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </vx-card>
        <!-- <h1>{{ item_data }}</h1> -->
    </div>
</template>
<script>
import { bus } from '../../main';
import 'swiper/dist/css/swiper.min.css'
import { swiper, swiperSlide } from 'vue-awesome-swiper'
import StarRating from 'vue-star-rating'
import axios from '../../axios'
import MultipleProductImages from '../../layouts/components/MultipleProductImages.vue';
export default {

    props: {
        id: {
            type: [Number,String]
        }
    },

    components: {
        StarRating,
        swiper,
    swiperSlide,
    "multiple-image-view":MultipleProductImages
    },
    computed:{
        isInCart() {
			const cart = this.$store.state.cart
			let cartItems=[]
			cart.forEach(element => {
				cartItems.push(element.productId)
			});
			console.log(cartItems,'cartitemss');
			return cartItems
		}
    },

    async mounted() {
        this.loggedIn = JSON.parse(localStorage.getItem('user-info'));
		this.userInfo(this.loggedIn);

        this.getwishlist()
        this.getCart()
        const getProducts = await axios.get('http://localhost:3000/products')
        this.productList = getProducts.data;

        const getProductDetail = await axios.get(`http://localhost:3000/products?id=${this.id}`)
        this.productDetails = getProductDetail.data;
 
    },

    name: 'customer-item-detail-view',
    data: () => ({
        productDetails       : '',
        productList          : '',
        img                  : '',
        name                 : '',
        desc                 : '',
        brand                : '',
        price                : '',
        rating               : '',
        wishlist             : '',
        available_item_colors: ["#7367F0", "#28C76F", "#EA5455", "#FF9F43", "#1E1E1E"]
    }),

    methods: {

        //////////////////////// USERINFO //////////////////////
		userInfo(loginInfo) {
			this.userId 	= loginInfo.id
			this.username 	= loginInfo.username
			
		},

        /////////////////// GETCART	///////////////////
		async getCart(){
			const res = await axios.get(`http://localhost:3000/cart?userId=${this.userId}`)
			this.cart = res.data
			// console.log('this.cart',this.cart);
			if(this.cart.length == 0){
				console.log('if length');
				this.cart=[{
					userId:this.userId,
					productId:'',
				}]
			}else{
				this.cart = res.data
				}
		},

        ////////////////////// GETWISHLIST /////////////////////
        async getwishlist(){
            const res = await axios.get(`http://localhost:3000/wishlist?userId=${this.userId}`)
				this.wishlist =	res.data
			if(this.wishlist.length == 0){
				this.wishlist=[{
					userId:this.userId,
					productId:'',
				}]
				}else{
				    this.wishlist = res.data
				}
		},

        ////////////////////// CHECK IN CART ////////////////////////////
		checkIfInCart(productid){
			let check = null;
            for (let i = 0; i < this.cart.length; i++) {
				check	=  productid == this.cart[i];
            }
            return check;
		},

		////////////////////// CHECK IF IN WISHLIST ////////////////////////////
		chekcIfinWishlist(productId){
			let check = null;
            for (let i = 0; i < this.wishlist.length; i++) {
				check	=  productId == this.wishlist[i];
            }
            return check;
		},


        /////////////////////////// TOGGLE WISHLIST //////////////////////////	
       async wishlistButtonToggle(productid,index) {
            const response = this.wishlist.filter((item)=>{
            return item.productId === productid
            })
            if(response.length == 0){
                    const item = {
				    	userId: this.userId,
				    	productId: productid
				    }
                await axios.post('http://localhost:3000/wishlist', item).then(res => {
				 	if(res){
						this.$vs.notify({
            			color    : 'success',
            			title    : 'Cart',
            			text     : 'Product added in wishlist',
            			position : 'top-right'
           			})
					
					    bus.$emit('refresh-wishList')
					    this.getwishlist()
					}
				}).catch(error => {
				this.$vs.notify({
					color    : 'danger',
					title    : 'Error',
					text     : 'unable to add to wishlist',
					position : 'top-right'
				    })	
				})
       		}
       		else{
				await axios.delete(`http://localhost:3000/wishlist/${response[0].id}`).then(res => {
				 	if(res){
					this.$vs.notify({
                	color    : 'danger',
                	title    : 'WishList',
                	text     : 'Product removed from wishlist',
                	position : 'top-right'
           			}) 
						bus.$emit('remove-from-wishlist',index)
						this.getwishlist()
					}
				}).catch(error => {
				this.$vs.notify({
					 color    : 'danger',
					 title    : 'Error',
					 text     : 'unable to remove from wishlist',
					 position : 'top-right'
				    })	
				})
      
            }
		},
    
		/////////////////////////// TOGGLE CART //////////////////////////	
		async toggleCartProduct(product) {
			const response =	this.cart.filter((item)=>{	
				return	item.productId === product.id
			})	
			if(response.length == 0){
				const item = {
						userId	    : this.userId,
						productId   : product.id,
						quantity    : 1,
						price       : product.price
					}
					await axios.post('http://localhost:3000/cart', item).then(res => {
					 	if(res){
							this.$vs.notify({
							color    : 'success',
							title    : 'Cart',
							text     : 'Product added in Cart',
							position : 'top-right'
					 	})	
						 this.getCart()
						  bus.$emit('refresh-data')
						 }
					
					}).catch(error => {
					this.$vs.notify({
						 color    : 'danger',
						 title    : 'Error',
						 text     : 'unable to add to cart',
						 position : 'top-right'
					 })	
					})
				}else{
				this.$router.push({
				name: "ecommerce-cart"
				});
			}
		}
    }
}
</script>