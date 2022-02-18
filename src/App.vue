<script>
import axios from "axios";
import UserProfile from './components/UserProfile.vue';
import Product from './components/Product.vue';
import SocketEvents from './socketEvents';
import io from 'socket.io-client';

const baseUrl = "http://localhost:3000";

const socket = io(baseUrl);


export default {
  components: { UserProfile, Product },
  name: 'App',
  data() {
    return {
      user: {
        username: '',
        avatar: '',
        id: 0
      },
      products: [],
      username: '',
      password: '',
      productName: '',
      info: '',
      forDelete: '',
      forUpdate: '',
      newName: '',
    }
  },

  methods: {
    async fetchUserData() {
      const res = await axios.get(`${baseUrl}/auth/get-user-data`, {
        withCredentials: true
      });
      this.user = res.data;
    },

    async fetchProducts() {
      const res = await axios.get(`${baseUrl}/products`, {
        withCredentials: true
      });
      this.products = res.data;
    },

    async login() {
      const res = await axios.post(`${baseUrl}/auth/login`, {
        username: this.username,
        password: this.password
      }, 
      {
        withCredentials: true
      });
      this.user = res.data;
    },

    async create() {
      const res = await axios.post(`${baseUrl}/users-products`, {
        product: {
    name: this.productName,
    description: "Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.",
    url: "https://gcdnb.pbrd.co/images/stIchWkZQ6vl.webp?o=1",
    color: "white",
    price: 49.99,
    brand: "zendure",
    category: "home/routers",
    shipment: 0,
    condition: "refurbished",
    discount: 20,
    hot: true,
    storage: "online"
        }
      }, {
        withCredentials: true
      });

      if(res.data.fullfilled) {
        const productId = res.data.product.id.toString();
        socket.emit(SocketEvents.CREATE_PRODUCT, {productId});
      }

   

    },

    async deleteProduct() {

      const res = await axios.delete(`${baseUrl}/users-products/${this.forDelete}`, {
        withCredentials: true
      });

      if(res.data.fullfilled) {
        socket.emit(SocketEvents.DELETE_PRODUCT, {productId: res.data.productId});
      }

    },

    async updateProduct() {

       const res = await axios.put(`${baseUrl}/users-products/${this.forUpdate}`, {
         product: {
           name: this.newName,
         }
       }, {
        withCredentials: true
      });

      if(res.data.fullfilled) {
        socket.emit(SocketEvents.UPDATE_PRODUCT, {productId: res.data.product.id});
      }

    }

  },

  beforeMount() {
    this.fetchUserData();
    this.fetchProducts();

    socket.on(SocketEvents.CONNECTION, socket => {
        console.log('connected');

    });

    socket.on(SocketEvents.GET_PRODUCT, (product)  => {
          this.products.push(product);
    });

    socket.on(SocketEvents.PRODUCT_DELETED, (productId)  => {
          const index = this.products.findIndex(product => product.id === productId);
          this.products.splice(index, 1);
    });

    socket.on(SocketEvents.PRODUCT_UPDATED, (product) => {
          const index = this.products.findIndex(p => p.id === product.id);
          this.products[index] = product;
    })

}
}
</script>

<template>

<div class="container">
<UserProfile v-bind:user="user" />

<div v-for="product in products" :key="product.id">
  <Product v-bind:product="product" />
</div>



<div class="login">
  <div>
  username <input type="text" class="username" v-model="username">
  </div>

  <div>
  password <input type="text" class="username" v-model="password">
  </div>

  <button @click="login">login</button>


  <div>
    <input type="text" placeholder="product-name" v-model="productName">
    <button @click="create">submit</button>
  </div>

<div>
    <input type="text" placeholder="for delete" v-model="forDelete">
    <button @click="deleteProduct">delete</button>
  </div>

  <div>
    <h2>Update</h2>
    <input type="text" placeholder="idul pentru udpate" v-model="forUpdate">
    <input type="text" placeholder="new name" v-model="newName">
    <button @click="updateProduct">update</button>
  </div>

</div>


</div>

<p style="font-size: 30px">{{ info }}</p>


</template>

<style>
*, *::before, *::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  width: 100vw;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.container * {
  margin: 20px;
}


</style>
