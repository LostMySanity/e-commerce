<!-- //////// Customer category and subcategory////////////// -->
<template>
    <div>
        <!-- add category model -->
        <add-category-modal @close-modal="close($event)" @refresh-subcategory="getsubCategory($event)" v-if="openCloseAddCategoryModal" :isSubcategory="isSubcategory" :isAddOrEdit="isAddOrEdit" :openCloseAddCategoryModal="openCloseAddCategoryModal" :editSubCategoryData="editSubCategoryData" />

        <vx-card class="w-full">
            <!-- category list -->
            <vs-table pagination max-items="10" search :data="subcategory">

                <template slot="header">
                    <h3 >
                        {{ category.label }} List
                    </h3>
        
                    <vs-spacer></vs-spacer>
                    <vs-button :to="{ name: 'customer-category-subcategory'}" class="mr-5 round" type="gradient">Back</vs-button>
                    <vs-button @click="addsubCategory(id)" class="mr-5 round" type="gradient">Add SubCategory</vs-button>
                </template>
                <template slot="thead">
                    <vs-th sort-key="subcategories">
                        subcategories
                    </vs-th>

                    <vs-th sort-key="joined">
                        Actions
                    </vs-th>
                </template>

                <template slot-scope="{data}">
                
                    <vs-tr :data="tr" :key="indextr" v-for="(tr, indextr) in data">
                        <vs-td :data="data[indextr].subcategory">
                            {{ data[indextr].subcategory }}
                        </vs-td>

                        <vs-td :data="data[indextr].action">
                            <div class="flex gap-2">
                                <feather-icon icon="Edit3Icon" svgClasses="h-5 w-5 mr-4 hover:text-primary cursor-pointer"
                                    @click.stop="editsubCategory(tr)" />
                                <feather-icon icon="Trash2Icon" svgClasses="h-5 w-5 hover:text-danger cursor-pointer"
                                    @click.stop="openConfirm(tr.id)" />
                            </div>
                        </vs-td>
                    </vs-tr>
                </template>
            </vs-table>
        </vx-card>
    </div>
</template>

<script>
import axios from '../../axios'

export default {
    props:{
        id:{
            type:Number
        }
    },

    data() {
        return {
            category:'',
            subcategories:"",
            categoryId:null,
            subcategory:'',
            editSubCategoryData:null,
            isSubcategory:false,
            openCloseAddCategoryModal:false,
            isAddOrEdit:false,
          
        }
    },
    components:{
    "add-category-modal":   ()  =>  import('./CustomerAddCategoryModal.vue')
    },

     mounted() {
        this.getsubCategory();
        this.getCategory();
    },

    methods: {

        async getsubCategory() {
            const response = await axios.get(`http://localhost:3000/subcategory?cat_id=${this.id}`)
            this.subcategory = response.data
        },

        async getCategory(){
            const res =  await axios.get(`http://localhost:3000/category?id=${this.id}`)
            this.category   =   res.data[0]
        },

        editsubCategory(editInfo){
            this.isSubcategory               =   true
            this.openCloseAddCategoryModal   =   true,
            this.isAddOrEdit                 =   false,
            this.editSubCategoryData         =   editInfo
        },

        addsubCategory(categoryId){
            this.isSubcategory               =   true
            this.openCloseAddCategoryModal   =   true,
            this.isAddOrEdit                 =   true
            this.editSubCategoryData         =   categoryId
        },

        ////////////////// DELETE DIALOG ///////////////
        openConfirm(categoryId){
        this.categoryId = categoryId
            this.$vs.dialog({
            type:'confirm',
            color: 'danger',
            title: `Delete`,
            text: 'Are you sure you wanna delete this Subcategory.',
            accept:this.acceptAlert
            })
        },

           async acceptAlert(){ 
            await axios.delete(`http://localhost:3000/subcategory/${this.categoryId}`).then(res =>   {
                if(res){
                    this.$vs.notify({
                        color:'danger',
                        title:'Deleted Category',
                        position:'top-right',
                        text:'The selected Subcategory was successfully deleted'
                    })
                    this.getsubCategory()
                }
            }).catch(error => {
                    this.$vs.notify({
                    color:'danger',
                    title:'Error',
                    position:'top-right',
                    text:'unable to delete'
                })
            })
       
        },

        close(){
            this.openCloseAddCategoryModal  =   false
        }
    }
}
</script>