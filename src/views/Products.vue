<template>
  <v-data-table
    :headers="headers"
    :items="products"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>Products</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ props }">
            <v-btn
              color="primary"
              dark
              class="addButton"
              v-bind="props"
            >
            <v-icon> mdi-folder-plus </v-icon> 
              New Product
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="model.product.name"
                      label="Name"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="model.product.price"
                      label="Price"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue-darken-1"
                variant="text"
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue-darken-1"
                variant="text"
                @click="add"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        size="small"
        class="me-2"
        @click="editItem(item.raw)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        size="small"
        @click="deleteItem(item.raw)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="getProducts"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>

<script>
import axios from 'axios'
import { VDataTable } from 'vuetify/labs/VDataTable'
  export default{
    components: {
    VDataTable,
 },
    name: 'products',
    data(){
      return {
        newDate: new Date().toJSON().slice(0, 10),
        dialog: false,
        dialogDelete: false,
        editedIndex: -1,
        editedItem: {
          name: '',
          price: '',
          createDate: ''
        },
        model: {
                    product:{
                      name: '',
                      price: '',
                      createDate: ''
                    },
                },    
        headers: [
        {
          title: 'Products',
          align: 'start',
          sortable: false,
          key: 'name',
        },
        { title: 'ID', key: 'id' },
        { title: 'Price', key: 'price' },
        { title: 'CreateDate', key: 'createDate' },
        { title: 'Actions', key: 'actions', sortable: false },
      ],
        products: []
      }
    },
    mounted(){
      this.getProducts();
      
    },
    computed: {
          formTitle () {
              return this.editedIndex === -1 ? 'New Product' : 'Edit Product'
            },
          },
    watch: {
            dialog (val) {
              val || this.close()
            },
            dialogDelete (val) {
              val || this.closeDelete()
            },
          },
          created () {
            this.getProducts()
          },
    methods: {
      getProducts(){
        axios.get('https://6479c08fa455e257fa63b224.mockapi.io/products').then(res => {
          this.products = res.data
        });
      },
      editItem (item) {
        this.editedIndex = this.products.indexOf(item)
        this.model.product = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        this.editedIndex = item.id
        this.dialogDelete = true
      },

      deleteItemConfirm () {
        axios.delete(`https://6479c08fa455e257fa63b224.mockapi.io/products/${this.editedIndex}`).then(res => {
            this.getProducts();
          })
          .catch(function (error) {
            if (error.response) {
              if(error.response.status == 404){
                alert('There is no product on that ID');
              }
            }
          })
        this.closeDelete()
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.model.product = Object.assign({}, this.editedItem)
          this.editedIndex = -1
          this.getProducts();
        })
      },

      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.model.product = Object.assign({}, this.editedItem)
          this.editedIndex = -1
        })
      },

      add(){
        if (this.editedIndex > -1) {
          axios.put(`https://6479c08fa455e257fa63b224.mockapi.io/products/${this.model.product.id}`, this.model.product).then(res => {
          });
        }
        else{
          this.model.product.createDate = this.newDate;
            axios.post('https://6479c08fa455e257fa63b224.mockapi.io/products', this.model.product).then(res => {
              this.getProducts();
                });
        }
        this.close();
      }
    }  
  }
</script>