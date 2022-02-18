<template>
  <div class="product">
    <img v-bind:src="product.url" alt="product" class="product-url">
    <div class="product-title">{{ product.name }}</div>
    <div>id : {{ product.id }}</div>
    <div>ownerId : {{ product.ownerId }}</div>
  </div>
</template>

<script>
export default {
  name: "Product",
  props: {
    product: Object
  }

}
</script>

<style>

.product {
  width: 200px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.product-url {
  display: block;
  width: 200px;
  height: 200px;
  margin-bottom: 10px;
}

.product-title {
  font-size: 20px;
}

</style>