<template>
    <div class="card border">
        <div class="card-header text-white" :class="isEditing ? 'bg-warning' : 'bg-primary'">
            <h5 class="mb-0">{{ isEditing ? 'Edit Product' : 'Add Product' }}</h5>
        </div>
        <div class="card-body">
            <form @submit.prevent="onSubmit">
                <div class="mb-3">
                    <label class="form-label">Product Name</label>
                    <input
                        type="text"
                        class="form-control"
                        v-model.trim="form.productName"
                        placeholder="Enter product name"
                        required
                    >
                </div>
                <div class="mb-3">
                    <label class="form-label">Product Price</label>
                    <input
                        type="number"
                        class="form-control"
                        v-model.number="form.productPrice"
                        placeholder="Enter product price"
                        min="0"
                        step="0.01"
                        required
                    >
                </div>
                <div class="mb-3">
                    <label class="form-label">Product Stock Status</label>
                    <select
                        class="form-select"
                        v-model="form.productStockStatus"
                        @change="onStockStatusChange"
                        required
                    >
                        <option>In Stock</option>
                        <option>Out of Stock</option>
                    </select>
                </div>
                <div class="mb-3">
                    <label class="form-label">Product Stock</label>
                    <input
                        type="number"
                        class="form-control"
                        v-model.number="form.productStock"
                        placeholder="Enter product stock"
                        min="0"
                        max="1000"
                        :disabled="isStockDisabled"
                        :class="{ 'bg-light': isStockDisabled }"
                        required
                    >
                    <div v-if="isStockDisabled" class="form-text text-muted">
                        <i class="fas fa-info-circle"></i> Stock input is disabled when "Out of Stock" is selected
                    </div>
                </div>
                <div class="d-grid gap-2">
                    <button type="submit" class="btn" :class="isEditing ? 'btn-warning' : 'btn-primary'">
                        {{ isEditing ? 'Update Product' : 'Add Product' }}
                    </button>
                    <button type="button" class="btn btn-secondary" @click="onReset">
                        {{ isEditing ? 'Cancel Edit' : 'Reset Form' }}
                    </button>
                </div>
            </form>
        </div>
    </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({ editingProduct: Object })
const emit = defineEmits(['add-product', 'update-product', 'cancel-edit'])

const emptyForm = () => ({
    productName: '',
    productPrice: 0,
    productStock: 0,
    productStockStatus: 'In Stock'
})

const form = ref(emptyForm())
const isEditing = computed(() => !!props.editingProduct)

// Computed property to determine if stock input should be disabled
const isStockDisabled = computed(() => form.value.productStockStatus === 'Out of Stock')

watch(
    () => props.editingProduct,
    product => { form.value = product ? { ...product } : emptyForm() },
    { immediate: true }
)

function onSubmit() {
    const f = form.value
    if (!f.productName.trim() || f.productPrice <= 0 || f.productStock < 0) return
    if (isEditing.value) emit('update-product', { ...f })
    else {
        emit('add-product', { ...f, id: Date.now() })
        form.value = emptyForm()
    }
}

function onReset() {
    isEditing.value ? emit('cancel-edit') : (form.value = emptyForm())
}

function onStockStatusChange() {
    // Automatically set stock to 0 when "Out of Stock" is selected
    if (form.value.productStockStatus === 'Out of Stock') {
        form.value.productStock = 0
    }
}
</script>

