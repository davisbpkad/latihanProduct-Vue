<template>
  <div class="container my-4">
    <header class="text-center mb-5">
      <h1 class="display-4 text-primary">Product-Vue</h1>
    </header>
    <div class="row justify-content-center">
      <div class="col-md-5 mb-4">
        <AddProduct
          :editing-product="editingProduct"
          @add-product="addProduct"
          @update-product="updateProduct"
          @cancel-edit="cancelEdit"
        />
      </div>
      <div class="col-md-7 mb-4">
        <ProductList
          :products="products"
          @delete-product="deleteProduct"
          @edit-product="startEdit"
        />
      </div>
    </div>
    <div id="products" class="d-flex flex-wrap gap-3"></div>
    <FetchProducts />
  </div>
</template>

<script>
import { ref, watch } from 'vue'
import AddProduct from './components/addProduct.vue'
import ProductList from './components/productList.vue'
import FetchProducts from './components/fetchProduct.vue'

export default {
  name: 'App',
  components: { AddProduct, ProductList, FetchProducts },
  setup() {
    const products = ref(JSON.parse(localStorage.getItem('products') || '[]'))
    const editingProduct = ref(null)

    watch(products, newProducts => {
      localStorage.setItem('products', JSON.stringify(newProducts))
    }, { deep: true })

    const addProduct = product => products.value.push(product)
    const startEdit = product => { editingProduct.value = { ...product } }
    const updateProduct = updatedProduct => {
      const index = products.value.findIndex(prod => prod.id === updatedProduct.id)
      if (index !== -1) products.value[index] = updatedProduct
      editingProduct.value = null
    }
    const cancelEdit = () => { editingProduct.value = null }
    const deleteProduct = id => {
      products.value = products.value.filter(product => product.id !== id)
      if (editingProduct.value?.id === id) editingProduct.value = null
    }

    // Fetch products from API and add to products list
    const fetchProducts = async () => {
      try {
        const res = await fetch('https://fakestoreapi.com/products')
        const data = await res.json()
        // Optionally merge or replace local products
        products.value = data.map(item => ({
          id: item.id,
          name: item.title,
          price: item.price,
          description: item.description
        }))
      } catch (e) {
        console.error('Failed to fetch products', e)
      }
    }

    return {
      products,
      editingProduct,
      addProduct,
      startEdit,
      updateProduct,
      cancelEdit,
      deleteProduct,
      fetchProducts
    }
  }
}
</script>
