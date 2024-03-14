<template>
  <div id="data-list-thumb-view" class="data-list-container">
    <vs-button class="mb-4" icon-pack="feather" to="/admin/partner" icon="icon-arrow-left"></vs-button>

      <!-- \\\\\\\\\\\\\\\\\ Single Product View \\\\\\\\\\\\\\\ -->
      <view-selected-product @close-modal="close($event)" :popup="popup"  :proData="proData" v-if="popup" />
      <vs-table  pagination max-items="10" :data="productList">
        <template slot="thead">
          <vs-th>Image</vs-th>
          <vs-th sort-key="name">Name</vs-th>
          <vs-th sort-key="category">Category</vs-th>
          <vs-th sort-key="price">Price</vs-th>
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

              <vs-td>
                <p class="product-category">{{ tr.category }}</p>
              </vs-td>

              <vs-td>
                <p class="product-price">${{ tr.price }}</p>
              </vs-td>

              <vs-td class="whitespace-no-wrap">
                <feather-icon icon="EyeIcon" svgClasses="w-5 h-5 hover:text-primary stroke-current"
                  @click.stop="viewProduct(tr)" />
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

  components:{
    "view-selected-product" : () => import('./AdminSingleProductView.vue')
  },

  data: () => ({
    productList: [],
    selected: '',
    proData:'',
    popup:false
  }),

  props: {
    id: {
      type: [Number, String]
    }
  },

  async mounted() {
    axios.get(`http://localhost:3000/products?partner_id=${this.id}`).then(res => this.productList = res.data)
  },

  methods: {
    viewProduct(selectedId) {
      this.proData   =  selectedId
      this.popup     =  true
    },

    close(val){
      console.log(val,'value');
      this.popup           =  val
    }
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
  
