<template>
    <div class="flex items-center justify-center">
        <vs-popup classContent="Add Products"
            :title="isAdd ? 'ADD Product' : 'EDIT Product'"
            :active.sync="isModalActiveLocal">
            <vx-card>
                   <!-- ///////////// REMOVE IMAGE POPUP /////////////// -->
                <vs-popup title="Remove Image" :active.sync="toggleremove">
                    <p>Are you sure you want to remove the selected image?</p>
                    <div class="flex">
                        <vs-button type="gradient" color="danger" class="round danger" @click="acceptAlert">Accept</vs-button><vs-button color="danger" type="gradient" class="round danger ml-3" @click="toggleremove = false">Cancel</vs-button>
                    </div>
                </vs-popup>
                <div class="vx-row mb-6 ">
                    <div class="vx-col w-full">
                        <template v-if="dataImg">

                            <!-- Image Container -->
                            <div class="img-container w-64 mx-auto flex items-center justify-center">
                                <img :src="dataImg" alt="img" class="responsive">
                            </div>

                            <!-- Image upload Buttons -->
                            <div class="modify-img flex justify-between mt-5">
                                <input type="file" class="hidden" ref="updateImgInput" @change="updateProductImage"
                                    accept="image/*">
                                <vs-button class="mr-4" type="flat" @click="$refs.updateImgInput.click()">Update
                                    Image</vs-button>
                                <vs-button type="flat" color="#999" @click="removeImage">Remove Image</vs-button>
                            </div>  
                        </template>

                        <div v-if="!dataImg" class="flex items-start flex-col sm:flex-row">
                            <div>
                                <span>Product Image</span>
                                <input v-validate="'required|image'" name="Image" ref="fileInput" style="display:none" type="file" class="hidden-input"
                                    @change="updateProductImage" accept="image/*" />
                                    <vs-button @click="$refs.fileInput.click()" type="border" class="mr-4">upload</vs-button>
                                    <span class="text-danger text-sm" v-show="errors.has('Image')">{{ errors.first('Image') }}</span>
                            </div>

                        </div>
                        <div class="vx-row mb-6">
                            <div class="vx-col w-full">
                                <vs-input class="w-full mt-2" label="Product Name" v-model.lazy="dataName"
                                    v-validate="'required|alpha_spaces'" data-vv-validate-on="blur" name="Product name" />
                                <span class="text-danger text-sm" v-show="errors.has('Product name')">{{ errors.first('Product name') }}</span>

                            </div>
                        </div>
                    </div>
                </div>
                <div class="vx-row mb-6">
                    <div class="vx-col w-full">
                        <vSelect placeholder="Category" name="Category" v-validate="'required'" v-model="dataCategory" class="form-control-select "
                            :options="category" @input="selectedCategory">
                        </vSelect>
                        <span class="text-danger text-sm" v-show="errors.has('Category')">{{ errors.first('Category') }}</span>

                        <vSelect placeholder="Subcategory" v-validate="'required'" name="Subcategory" v-model="dataSubCategory" class="form-control-select  mt-2"
                            :options="subcategory">
                        </vSelect><span class="text-danger text-sm" v-show="errors.has('Subcategory')">{{ errors.first('Subcategory') }}</span>
                    </div>
                </div>
                <div class="vx-row mb-6">
                    <div class="vx-col w-full">
                        <vs-textarea v-validate="'required'" data-vv-validate-on="blur" class="mt-2 " v-model="dataDesc" name="Description" label="Product Description"
                            placeholder="Description" />
                            <span class="text-danger text-sm" v-show="errors.has('Description')">{{ errors.first('Description') }}</span>

                        <vs-input class="mt-2 " v-validate="'required|numeric'" data-vv-validate-on="blur" v-model="dataPrice" label="Price" name="Price" placeholder="Price" />
                        <span class="text-danger text-sm" v-show="errors.has('Price')">{{ errors.first('Price') }}</span>

                        <vs-input class="mt-2" name="Quantity" v-validate="'required|numeric'" v-model="dataQuan" data-vv-validate-on="blur" label="Quantity"
                        placeholder="Quantity" /><span class="text-danger text-sm" v-show="errors.has('Quantity')">{{ errors.first('Quantity') }}</span>

                    </div>
                    <div>
                        <vs-button @click="isAdd ? submit() : edit()" class="round mt-4 submit" type="gradient">Submit</vs-button>
                        <vs-button @click="isModalActiveLocal = false" class="round mt-4 submit"
                        type="gradient">Close</vs-button>
                    </div>
                </div>
            </vx-card>
        </vs-popup>
    </div>
</template>   

<script>

import vSelect from 'vue-select'
import axios from '../../axios';
export default {
    props: {

        isModalActive: {
            type: Boolean,
            required: true
        },
        modalData: {
            type: Object,  
        },
        isAdd:{
            type:Boolean
        }

    },
    data: () => ({
        subcategory: [],
        activePrompt:false,
        toggleremove:false,
        partnerId:null,
        removeImageId:null,
        category:[],
        dataId: null,
        dataName: "",
        dataCategory: null,
        dataSubCategory: null,
        dataImg: null,
        dataOrder_status: "",
        dataPrice: null,
        dataDesc: "",
        dataQuan: ''

    }),
    components: {
        vSelect
    },

    mounted(){
    this.getCategory()
    this.loggedIn = JSON.parse(localStorage.getItem('user-info'))
    this.userInfo(this.loggedIn);
       this.isAddOrEdit()
    },
    computed: {

        isModalActiveLocal: {
            get() {
                return this.isModalActive
            },
            set(val) {
                if (!val) {
                    this.$emit('close-modal')
                }
            }
        }
    },
    methods: {
        isAddOrEdit(){
            if(this.isAdd  == true){
            this.dataId           = null
            this.dataName         = ""
            this.dataCategory     = null
            this.dataSubCategory  = null
            this.dataOrder_status = "pending"
            this.dataPrice        = null
            this.dataImg          = null
            this.dataDesc         = ""
            this.dataQuan         = ""
        }else{
                this.dataId            = this.modalData.id
                this.dataCategory      = this.modalData.category
                this.dataSubCategory   = this.modalData.subcategory
                this.dataImg           = this.modalData.img
                this.dataName          = this.modalData.name
                this.dataOrder_status  = this.modalData.order_status
                this.dataPrice         = this.modalData.price
                this.dataDesc          = this.modalData.description
                this.dataQuan          = this.modalData.quantity
        }
        },

        userInfo(loginInfo){
        console.log(loginInfo);
        this.partnerId = loginInfo.id
         },

        updateProductImage(e) {
        const selectedImage = e.target.files[0];
            this.createBase64Image(selectedImage);
        },
        createBase64Image(fileObject) {
            console.log(fileObject);
            const reader = new FileReader();
            reader.onload = (e) => {
                this.dataImg = e.target.result;
            };
            reader.readAsDataURL(fileObject);
        },  

            /////////////////// CATEGORY AND SUBCATEGORY    /////////////////
         async getCategory(){
          await axios.get(`http://localhost:3001/category`).then(res  => this.category =  res.data);
         },
         
        async selectedCategory(event) { 
            console.log(event,'event');
        
            await axios.get(`http://localhost:3001/subcategory?cat_id=${event.id}`).then(res  => this.subcategory  =  res.data);
        },

         ////////////////////////// REMOVE AVATAR  //////////////////////////
         removeImage() {
            if(this.isAdd == true){
                this.dataImg        = null
            }else{
                this.removeImageId  =  this.dataId
                this.toggleremove   = true
            }
        },

        acceptAlert() {
            this.finalRemove(this.removeImageId);
            this.toggleremove = false
        },
        
      async finalRemove(){
        const image = "";
        this.dataImg = null;
        const removeProfile = {
            partner_id   : this.partnerId,       
            name         : this.dataName,     
            category     : this.dataCategory.label,  
            subcategory  : this.dataSubCategory.label ,
            order_status : this.dataOrder_status,
            price        : this.dataPrice, 
            img          : this.dataImg,   
            description  : this.dataDesc,   
            quantity     : this.dataQuan,  
            }
         await axios.patch(`http://localhost:3000/products/${this.removeImageId}`, removeProfile).then(
            res => {
                if(res){
                this.$vs.notify({
                color: 'danger',
                title: 'Delete',
                text: 'The selected image was successfully removed',
                position:'top-right'
                })
                this.$emit('refresh')
                }
            }
         ).catch(error => {
            this.$vs.notify({
                color: 'danger',
                title: 'Error',
                text: 'The selected image wasnt able to be removed',
                position:'top-right'
            })
         })
    
        },

        ///////////////// ADD OR EDIT SECTION ////////////////
        submit() {
            this.$validator.validateAll().then(async result =>  {
                if(result){
                    const productData={
                        partner_id   : this.partnerId,       
                        name         : this.dataName,     
                        category     : this.dataCategory.id,  
                        subcategory  : this.dataSubCategory.id ,
                        order_status : this.dataOrder_status,
                        price        : this.dataPrice, 
                        img          : this.dataImg,   
                        description  : this.dataDesc,   
                        quantity     : this.dataQuan,   
                    };
                    await axios.post(`http://localhost:3000/products`,productData)
                    this.close();
                    this.$emit('refresh') 
                }
            })
        },
        edit(){
            this.$validator.validateAll().then(async result =>  {
                if(result){
                    const productData={
                        partner_id   : this.partnerId, 
                        id           : this.dataId,   
                        name         : this.dataName,     
                        category     : this.dataCategory.id,  
                        subcategory  : this.dataSubCategory.id ,
                        order_status : this.dataOrder_status,
                        price        : this.dataPrice, 
                        img          : this.dataImg,   
                        description  : this.dataDesc,   
                        quantity     : this.dataQuan,   
                    };
                    await axios.patch(`http://localhost:3000/products/${productData.id}`,productData)
                    this.close();
                    this.$emit('refresh') 
                }
            })
        },
        close() {
            this.$emit('close-modal')
        }
    },
}

</script>

<style>
.vs-dialog{
    z-index: 999 !important;
    
}

</style>