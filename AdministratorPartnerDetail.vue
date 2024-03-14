<template>
    <div id="page-user-view">
        <div id="user-data">
            <div>
             <vs-button class="mb-4" icon-pack="feather" to="/admin/partner" icon="icon-arrow-left"></vs-button>
            </div>
            <vx-card title="Account" class="mb-base">
                <!-- Avatar -->
                <div class="vx-row">
                    <div class="vx-col" id="avatar-col">
                        <div class="img-container mb-4">
                            <img :src="userInfo.img" class="rounded w-full" />
                        </div>
                    </div>
                    <!-- Information-->
                    <div class="vx-col flex-1" id="account-info-col-1">
                        <table>
                            <tr>
                                <td class="font-semibold">Username</td>
                                <td>{{ userInfo.username }}</td>
                            </tr>
                            <tr>
                                <td class="font-semibold">Name</td>
                                <td>{{ userInfo.name }}</td>
                            </tr>
                            <tr>
                                <td class="font-semibold">Email</td>
                                <td>{{ userInfo.email }}</td>
                            </tr>
                        </table>
                    </div>
                    <div class="vx-col flex-1" id="account-info-col-2">
                        <table>
                            <tr>
                                <td class="font-semibold">Status</td>
                                <td>{{ userInfo.status }}</td>
                            </tr>
                            <tr>
                                <td class="font-semibold">Role</td>
                                <td>{{ userInfo.role }}</td>
                            </tr>
                            <tr>
                                <td class="font-semibold">Company</td>
                                <td>{{ userInfo.company }}</td>
                            </tr>
                        </table>
                    </div>
                </div>
            </vx-card>

            <div class="vx-row">
                <div class="vx-col  w-full">
                    <vx-card title="Information" class="mb-base">
                        <table>
                            <tr>
                                <td class="font-semibold">Joined in</td>
                                <td>{{ userInfo.joined }}</td>
                            </tr>
                            <tr>
                                <td class="font-semibold">Mobile</td>
                                <td>{{ userInfo.mobileno }}</td>
                            </tr>
                            <tr>
                                <td class="font-semibold">Website</td>
                                <td>{{ userInfo.website }}</td>
                            </tr>
                        </table>
                    </vx-card>
                </div>     
            </div> 
        </div>
    </div>
</template>

<script>
 import AdminSingleProductView from './AdminSingleProductView.vue';
import axios from '../../axios';
export default {
    components: {
        AdminSingleProductView
    },

    props: {
        id: {
            type: [Number,String]
        }
    },

    data() {
        return {
            productList:{},
            userInfo:{},
            popup: null,
            itemData:false,
            proData: {
                category: null,
                img: null,
                name: null,
                desc: null,
                subcategory: null,
                inventory: null,
                order_status: null,
                popularity: null,
                price: null,
                objectID: null,
                wishlist: null
            }
        }
    },

   async mounted() {
    await axios.get(`http://localhost:3000/login?id=${this.id}`).then(res => this.userInfo = res.data[0]);
   },

    methods: { 
        close() {
            this.popup = false
        }
    }
}

</script>

<style lang="scss">
#avatar-col {
    width: 10rem;
}

#page-user-view {
    table {
        td {
            vertical-align: top;
            min-width: 140px;
            padding-bottom: .8rem;
            word-break: break-all;
        }

        &:not(.permissions-table) {
            td {
                @media screen and (max-width:370px) {
                    display: block;
                }
            }
        }
    }
}

@media screen and (min-width:1201px) and (max-width:1211px),
only screen and (min-width:636px) and (max-width:991px) {
    #account-info-col-1 {
        width: calc(100% - 12rem) !important;
    }

}
</style>
  