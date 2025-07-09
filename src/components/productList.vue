<template>
    <div class="card border">
        <div class="card-header bg-success text-white">
            <h5 class="mb-0">Product List ({{ products.length }} items)</h5>
        </div>
        <div class="card-body">
            <template v-if="products.length">
                <ProductItem
                    v-for="product in products"
                    :key="product.id"
                    :product="product"
                    @delete-product="$emit('delete-product', $event)"
                    @edit-product="$emit('edit-product', $event)"
                    class="mb-3"
                />
            </template>
            <template v-else>
                <div class="text-center text-muted py-4">
                    <i class="fas fa-box-open fa-3x mb-3"></i>
                    <p class="fs-5">No products available</p>
                    <p class="text-muted">Add your first product to get started!</p>
                </div>
            </template>
        </div>
    </div>
</template>

<script setup>
import ProductItem from './ProductItem.vue'

defineProps({
    products: {
        type: Array,
        required: true,
        default: () => []
    }
})

defineEmits(['delete-product', 'edit-product'])
</script>
