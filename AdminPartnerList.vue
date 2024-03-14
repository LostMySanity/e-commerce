<template >
    <div>
        <add-edit-partner-modal @close-modal="toggleDataModal" @refresh-partner-list="refresh($event)" :isEdit="isEdit"
            :partnerDetails="partnerDetails" :isOpen="isOpen" v-if="isOpen" />
                <vx-card class="w-full">
                    <div>
                        <vs-table @input="selectedPartners" v-model="selected" pagination max-items="10" search
                            :data="partnerList">
                            <template slot="header">
                                <h3>
                                    Partner List
                                </h3>
                                <vs-spacer></vs-spacer>
                                <vs-button :to="{name: 'category'}" class="mr-5 round" type="gradient">Show Category</vs-button>
                                <vs-button @click="addPartner" class="mr-5 round" type="gradient">Add Partner</vs-button>
                            </template>
                            <template slot="thead">
                                <vs-th>
                                    Profile
                                </vs-th>
                                <vs-th sort-key="username">
                                    Name
                                </vs-th>
                                <vs-th sort-key="email">
                                    Email
                                </vs-th>
                                <vs-th sort-key="website">
                                    Website
                                </vs-th>
                                <vs-th sort-key="username">
                                    Username
                                </vs-th>
                                <vs-th sort-key="status">
                                    Status
                                </vs-th>
                                <vs-th sort-key="actions">
                                    Joined In
                                </vs-th>
                                <vs-th sort-key="product">
                                    Product
                                </vs-th>
                                <vs-th sort-key="joined">
                                    Actions
                                </vs-th>
                            </template>

                            <template slot-scope="{data}">
                                <vs-tr :data="tr" :key="indextr" v-for="(tr, indextr) in data">
                                    <vs-td :data="data[indextr].img">
                                        <vs-avatar :src="data[indextr].img" />
                                    </vs-td>
                                    <vs-td :data="data[indextr].name">
                                        {{ data[indextr].name }}
                                    </vs-td>
                                    <vs-td :data="data[indextr].email">
                                        {{ data[indextr].email }}
                                    </vs-td>
                                    <vs-td :data="data[indextr].website">
                                        {{ data[indextr].website }}
                                    </vs-td>
                                    <vs-td :data="data[indextr].username">
                                        {{ data[indextr].username }}
                                    </vs-td>
                                    <div>
                                        <vs-chip class="ag-grid-cell-chip" :color="chipColor(data[indextr].status)">
                                            <vs-td :data="data[indextr].status">
                                                {{ data[indextr].status }}
                                            </vs-td>
                                        </vs-chip>
                                    </div>
                                    <vs-td :data="data[indextr].joined">
                                        {{ data[indextr].joined }}
                                    </vs-td>
                                    <vs-td :data="data[indextr].product">
                                        {{ data[indextr].product }}
                                    </vs-td>

                                    <vs-td :data="data[indextr].action">
                                        <div class="flex gap-2">
                                            <feather-icon icon="Edit3Icon"
                                                svgClasses="h-5 w-5 mr-4 hover:text-primary cursor-pointer"
                                                @click.stop="editPartner(tr)" />
                                            <feather-icon icon="Trash2Icon"
                                                svgClasses="h-5 w-5 hover:text-danger cursor-pointer"
                                                @click.stop="deletePartner(tr.id)" />
                                         <vs-button @click.stop="productView(tr.id)" type="gradient"  class="ml-4">Product</vs-button>
                                    </div>
                                </vs-td>
                            </vs-tr>
                        </template>
                    </vs-table>
                </div>
        </vx-card>
    </div>
</template>

<script>

import vSelect from 'vue-select'
import axios from 'axios';
export default {
    components: {
        vSelect,
        "add-edit-partner-modal"    : () => import('@/layouts/components/AdminAddAndEditPartnerModal.vue'),
        "admin-partner-product-view": () => import('./AdministratorPartnerDetail.vue')
    },

    data: () => ({
        partnerDetails:{},  
        isEdit:null,
        fileName: '',
        partnerList: '',
        selected: [],
        isOpen: false,
        product:
        {
            name: null,
            email: null,
            date: null,
            address: null,
            username: null,
            profilepic: null,
            website: null
        },
        title: '',
        deleteUserId:null,

    }),

    mounted() {
        this.refresh()
    },

    computed: {

        chipColor() {
            return (type) => {
                if (type === "active") return "success"
                else if (type === "blocked") return "danger"
                else if (type === "deactivated") return "warning"
                else "primary"
            }
        },
    },

    methods: {
        refresh() {
            axios.get(`http://localhost:3000/login?role=${'partner'}`).then(response => (this.partnerList = response.data))
        },

        ///////////////////////    PRODUCT VIEW    ////////////////////
        productView(partnerId){
            this.$router.push({
                name    : 'admin-partner-product',
                params  : { id: partnerId }
            });
        },

        //////////////////////  PARTNER DETAIL VIEW   /////////////////
        selectedPartners() {
            const selected_partner_data = this.selected;
            this.$router.push({
                name: "admin-partner-detail",
                params: { id: selected_partner_data.id }
            });
        },

         //////////////////  DELETE SECTION //////////////////////
        deletePartner(deleteId) {
            this.deleteUserId   =  deleteId
            this.$vs.dialog({
                type: 'confirm',
                color: 'danger',
                text: 'Confirm Delete this Partner',
                accept: this.acceptAlert
            })
        },

        acceptAlert() {
            this.$vs.notify({
                color: 'danger',
                title: 'Delete',
                text: 'The selected Partner was successfully deleted',
                position:'top-right'
            })
            this.finalDelete(this.deleteUserId)
        },

        async finalDelete(deleteId) {
            await axios.delete(`http://localhost:3000/login/${deleteId}`)
            this.refresh()
        },
        
        //////////////////  ADD, EDIT AND OPEN CLOSE MODAL //////////////////////
        
        addPartner() {
            this.partnerDetails  =   this.partnerDetails,
            this.isEdit          =   true
            this.toggleDataModal(true);
        },

        editPartner(partnerdetails) {
            this.partnerDetails  =   partnerdetails,
            this.isEdit          =   false
            this.toggleDataModal(true);
        },

        toggleDataModal(val = false) {
            this.isOpen = val
        },
        
        close() {
            this.isOpen = false
        },
    }

}

</script>

<style lang="scss" scpoped>
.ag-grid-cell-chip {
    &.vs-chip-success {
        background: rgba(var(--vs-success), .15);
        color: rgba(var(--vs-success), 1) !important;
        font-weight: 500;
    }

    &.vs-chip-warning {
        background: rgba(var(--vs-warning), .15);
        color: rgba(var(--vs-warning), 1) !important;
        font-weight: 500;
    }

    &.vs-chip-danger {
        background: rgba(var(--vs-danger), .15);
        color: rgba(var(--vs-danger), 1) !important;
        font-weight: 500;
    }
}
</style>
