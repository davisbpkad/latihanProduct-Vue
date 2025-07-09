<template>
    <div class="card product-item-card">
    <div class="card-body">
        <div class="d-flex flex-column">
            <h5 class="card-title mb-2">{{ product.productName }}</h5>
            <p class="text-primary mb-2">Rp{{ formattedPrice }}</p>
            <p class="text-info mb-2">Stock: {{ product.productStock }}</p>
            <div class="mb-3">
                <span class="badge" :class="statusBadgeClass">
                    {{ product.productStockStatus }}
                </span>
            </div>
           <div class="d-grid gap-2">
                <button
                    @click="editProduct"
                    class="btn btn-outline-warning"
                    :disabled="isDeleting"
                >
                    <i class="fas fa-edit me-1"></i>
                    Edit
                </button>
                <button
                    @click="deleteProduct"
                    class="btn btn-outline-danger"
                    :disabled="isDeleting"
                >
                    <i class="fas fa-trash-alt me-1"></i>
                    {{ isDeleting ? 'Deleting...' : 'Delete' }}
                </button>
        </div>
        </div>
    </div>
</div>
</template>

<script setup>
import { computed, ref } from 'vue'

const props = defineProps({ product: Object })
const emit = defineEmits(['delete-product', 'edit-product'])

const isDeleting = ref(false)

const formattedPrice = computed(() =>
    Number(props.product.productPrice).toLocaleString('id-ID', { minimumFractionDigits: 2 })
)

const statusBadgeClass = computed(() => ({
    'bg-success': props.product.productStockStatus === 'In Stock',
    'bg-danger': props.product.productStockStatus === 'Out of Stock',
    'bg-secondary': !['In Stock', 'Out of Stock'].includes(props.product.productStockStatus)
}))

function editProduct() {
    emit('edit-product', props.product)
}

async function deleteProduct() {
    if (!confirm(`Delete "${props.product.productName}"?`)) return
    isDeleting.value = true
    await new Promise(r => setTimeout(r, 300))
    emit('delete-product', props.product.id)
    isDeleting.value = false
}
</script>

