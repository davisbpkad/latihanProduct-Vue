<template>
  <div class="container my-4">
    <header class="text-center mb-5">
      <h1 class="display-4 text-primary">Product-Vue</h1>
      <p class="lead text-muted">Manage your products efficiently</p>
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
    </div>
    <div class="row justify-content-center">
      <div class="col-md-7 mb-4">
        <ProductList
          :products="products"
          @delete-product="deleteProduct"
          @edit-product="startEdit"
        />
      </div>
    </div>
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

    const addProduct = product => {
      // Ensure consistent data structure
      const newProduct = {
        id: Date.now(),
        productName: product.productName,
        productPrice: product.productPrice,
        productStock: product.productStock,
        productStockStatus: product.productStockStatus,
        productDescription: product.productDescription || '',
        productImage: product.productImage || '',
        createdAt: new Date().toISOString()
      }
      products.value.push(newProduct)
      console.log(`Product "${newProduct.productName}" added successfully!`)
    }

    const startEdit = product => { 
      editingProduct.value = { ...product }
      console.log(`Editing product: ${product.productName}`)
    }

    const updateProduct = updatedProduct => {
      const index = products.value.findIndex(prod => prod.id === updatedProduct.id)
      if (index !== -1) {
        products.value[index] = updatedProduct
        console.log(`Product "${updatedProduct.productName}" updated successfully!`)
      }
      editingProduct.value = null
    }

    const cancelEdit = () => { 
      editingProduct.value = null
      console.log('Edit cancelled')
    }

    const deleteProduct = id => {
      const productToDelete = products.value.find(p => p.id === id)
      products.value = products.value.filter(product => product.id !== id)
      if (editingProduct.value?.id === id) editingProduct.value = null
      console.log(`Product "${productToDelete?.productName}" deleted successfully!`)
    }

    // Enhanced fetch function with proper field mapping
    const fetchProducts = async () => {
      try {
        const res = await fetch('https://fakestoreapi.com/products')
        const data = await res.json()
        
        // Map to consistent structure
        const apiProducts = data.map(item => ({
          id: `api-${item.id}`, // Prefix to avoid ID conflicts
          productName: item.title,
          productPrice: item.price,
          productStock: Math.floor(Math.random() * 100) + 1,
          productStockStatus: 'In Stock',
          productDescription: item.description,
          productImage: item.image,
          category: item.category,
          rating: item.rating,
          source: 'fakestore-api',
          importedAt: new Date().toISOString()
        }))
        
        // Add to existing products instead of replacing
        products.value = [...products.value, ...apiProducts]
        console.log(`Fetched and added ${apiProducts.length} products from API`)
        
      } catch (e) {
        console.error('Failed to fetch products', e)
        alert('Failed to fetch products from API')
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

<style scoped>
.container {
  max-width: 1200px;
}

.display-4 {
  font-weight: 700;
}

.lead {
  font-size: 1.1rem;
}
</style>
