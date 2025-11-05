<template>
  <main class="page-wrapper">
    <div class="product-card z-depth-2">
      <h3 class="center-align teal-text text-darken-2 title">
        <i class="material-icons small">shopping_bag</i>
        <span> Product Management</span>
      </h3>

      <!-- Form thêm sản phẩm -->
      <form @submit.prevent="addProduct" class="row form-row">
        <div class="input-field col s12 m5">
          <input v-model="newProduct.name" id="name" type="text" required />
          <label for="name">Product Name</label>
        </div>

        <div class="input-field col s12 m5">
          <input v-model.number="newProduct.price" id="price" type="number" required />
          <label for="price">Price ($)</label>
        </div>

        <div class="input-field col s12 m2 center-align">
          <button class="btn waves-effect waves-light teal darken-2 full-btn" type="submit">
            <i class="material-icons">add</i>
          </button>
        </div>
      </form>

      <!-- Bảng sản phẩm -->
      <div class="table-wrapper">
        <table class="highlight centered responsive-table">
          <thead class="teal lighten-2 white-text">
            <tr>
              <th>Name</th>
              <th>Price ($)</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="p in products" :key="p._id">
              <td>{{ p.name }}</td>
              <td>{{ p.price.toLocaleString() }}</td>
              <td>
                <button
                  class="btn-small red lighten-1 waves-effect waves-light"
                  @click="deleteProduct(p._id)"
                >
                  <i class="material-icons">delete</i>
                </button>
              </td>
            </tr>
          </tbody>
        </table>

        <p v-if="products.length === 0" class="grey-text center-align" style="margin-top: 15px;">
          No products found. Please add one!
        </p>
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const backendURL = 'http://localhost:3000/api/products'
const products = ref([])
const newProduct = ref({ name: '', price: '' })

const fetchProducts = async () => {
  const res = await axios.get(backendURL)
  products.value = res.data
}

const addProduct = async () => {
  if (!newProduct.value.name || !newProduct.value.price) return
  await axios.post(backendURL, newProduct.value)
  newProduct.value = { name: '', price: '' }
  fetchProducts()
  M.toast({ html: '✅ Product added successfully!' })
}

const deleteProduct = async (id) => {
  if (confirm('Delete this product?')) {
    await axios.delete(`${backendURL}/${id}`)
    fetchProducts()
    M.toast({ html: '🗑️ Deleted successfully!' })
  }
}

onMounted(fetchProducts)
</script>

<style>
/* Toàn bộ trang căn giữa theo chiều ngang và dọc */
.page-wrapper {
  min-height: 100vh;
  display: center;
  align-items: flex-start;
  justify-content: center;
  background: linear-gradient(180deg, #e0f7fa 0%, #f5f5f5 100%);
  padding: 50px 20px;
}

/* Thẻ chính chứa form + bảng */
.product-card {
  width: 100%;
  max-width: 800px;
  background: #fff;
  border-radius: 16px;
  display:"center";
  justify-content: center;
  padding: 30px;
  box-shadow: 0 4px 15px rgba(16, 9, 9, 0.1);
}

/* Tiêu đề */
.title {
  font-weight: 600;
  margin-bottom: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 8px;
}

/* Form căn đều */
.form-row {
  margin-bottom: 50px;
}

/* Nút thêm full chiều rộng cột */
.full-btn {
  width: 100%;
  height: 56px;
  font-size: 18px;
  border-radius: 8px;
}

/* Bảng */
.table-wrapper {
  overflow-x: auto;
  justify-content: center;
  display: flex;
}

/* Responsive đẹp hơn */
@media (max-width: 600px) {
  .product-card {
    padding: 20px;
  }
  h3.title {
    font-size: 1.8rem;
  }
}
</style>
