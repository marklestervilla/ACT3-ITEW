<template>
  <div class="container py-5">
    <h1 class="mb-4">Shopping Cart</h1>

    <!-- Product Form -->
    <form @submit.prevent="addNewProduct" class="mb-4">
      <div class="form-group">
        <label for="productName">Product Name</label>
        <input type="text" id="productName" v-model="newProduct.name" class="form-control" required>
      </div>
      <div class="form-group">
        <label for="productPrice">Price</label>
        <input type="number" id="productPrice" v-model.number="newProduct.price" class="form-control" min="0" step="0.01" required>
      </div>
      <button type="submit" class="btn btn-primary">Add Product</button>
    </form>

    <!-- Product List -->
    <h2>Product List:</h2>
    <div class="row">
      <div v-for="(product, index) in products" :key="index" class="col-md-4 mb-4">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">{{ product.name }}</h5>
            <p class="card-text">Price: ₱{{ product.price }}</p>
            <button v-if="product.id !== editingProductId" @click="addToCart(product, product.quantityToAdd)" class="btn btn-success">Add to Cart</button>
            <input v-else type="number" v-model="product.quantityToAdd" class="form-control mb-2" min="1" step="1">
          </div>
        </div>
      </div>
    </div>

    <!-- Cart -->
    <h2>Cart:</h2>
    <div class="cart">
      <div v-for="(item, index) in cart" :key="index" class="card mb-3">
        <div class="card-body">
          <h5 class="card-title">{{ item.name }}</h5>
          <p class="card-text">Price: ₱{{ item.price }} - Quantity: {{ item.quantity }}</p>
          <input type="number" v-model="item.quantity" class="form-control mb-2" min="1" @change="updateQuantity(index, item.quantity)">
          <button @click="removeFromCart(index)" class="btn btn-danger">Remove</button>
        </div>
      </div>
      <p class="total">Total: ₱{{ totalPrice }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: 'Coke', price: 50.50, quantityToAdd: 1 },
        { id: 2, name: 'Pepsi', price: 65.10, quantityToAdd: 1 },
        { id: 3, name: 'Mountain Due', price: 20.76, quantityToAdd: 1 },
        { id: 4, name: 'Sprite', price: 40.05, quantityToAdd: 1 },
        { id: 5, name: 'Root Beer', price: 80.05, quantityToAdd: 1 },
        { id: 6, name: 'Zesto', price: 20.05, quantityToAdd: 1 }
      ],
      newProduct: {
        name: '',
        price: null
      },
      editingProductId: null,
      editedProduct: {
        id: null,
        name: '',
        price: null
      }
    };
  },
  props: ['cart', 'router'],
  computed: {
    totalPrice() {
      return this.cart.reduce((total, item) => total + (item.price * item.quantity), 0);
    }
  },
  methods: {
    addToCart(product, quantity) {
      const isAuthenticated = localStorage.getItem('token');
      if (isAuthenticated) {
        let existingItem = this.cart.find(item => item.id === product.id);
        if (existingItem) {
          existingItem.quantity += quantity;
        } else {
          this.$emit('add-to-cart', { id: product.id, name: product.name, price: product.price, quantity });
        }
      } else {
        this.$emit('navigate-to-login');
      }
    },
    removeFromCart(index) {
      this.$emit('remove-from-cart', index);
    },
    updateQuantity(index, quantity) {
      if (quantity < 1) {
        this.$emit('remove-from-cart', index);
      }
    },
    addNewProduct() {
      if (this.newProduct.name && this.newProduct.price) {
        this.products.push({
          id: this.products.length + 1,
          name: this.newProduct.name,
          price: parseFloat(this.newProduct.price),
          quantityToAdd: 1
        });
        this.newProduct.name = '';
        this.newProduct.price = null;
      }
    }
  }
}
</script>

<style scoped>
.total {
  font-weight: bold;
  font-size: 18px;
}
</style>
