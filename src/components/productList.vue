<template>
  <div class="card">
    <div class="card-header bg-success text-white">
      <h5 class="mb-0">
        <i class="fas fa-list"></i> Product List 
        <span class="badge bg-light text-dark ms-2">{{ products.length }}</span>
      </h5>
    </div>
    <div class="card-body">
      <div v-if="products.length === 0" class="text-center text-muted py-4">
        <i class="fas fa-box-open fa-3x mb-3"></i>
        <p>No products available. Add your first product!</p>
      </div>
      <div v-else class="row">
        <div v-for="product in products" :key="product.id" class="col-md-6 col-lg-4 mb-3">
          <ProductItem 
            :product="product" 
            @delete-product="handleDelete"
            @edit-product="handleEdit"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ProductItem from './ProductItem.vue'

export default {
  name: 'ProductList',
  components: {
    ProductItem
  },
  props: {
    products: {
      type: Array,
      required: true
    }
  },
  emits: ['delete-product', 'edit-product'],
  methods: {
    handleDelete(productId) {
      this.$emit('delete-product', productId)
    },
    handleEdit(updatedProduct) {
      this.$emit('edit-product', updatedProduct)
    }
  }
}
</script>

<style scoped>
.card {
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.fa-box-open {
  opacity: 0.5;
}
</style>
