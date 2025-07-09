<template>
  <div class="card product-item-card h-100">
    <!-- Product Image -->
    <div class="card-img-wrapper" v-if="product.productImage">
      <img 
        :src="product.productImage" 
        class="card-img-top product-image" 
        :alt="product.productName"
        @error="handleImageError"
      >
    </div>
    <div v-else class="card-img-placeholder">
      <i class="fas fa-image fa-3x text-muted"></i>
    </div>

    <div class="card-body d-flex flex-column">
      <!-- Product Header -->
      <div class="product-header mb-3">
        <h5 class="card-title product-name">{{ product.productName }}</h5>
        <div class="product-meta">
          <span v-if="product.source" class="badge bg-info me-1">
            {{ product.source === 'fakestore-api' ? 'API' : 'Local' }}
          </span>
          <span v-if="product.category" class="badge bg-secondary">
            {{ product.category }}
          </span>
        </div>
      </div>

      <!-- Product Description -->
      <div v-if="product.productDescription" class="product-description mb-3">
        <p class="card-text text-muted">
          {{ truncatedDescription }}
          <button 
            v-if="product.productDescription.length > 100" 
            class="btn btn-link btn-sm p-0 ms-1"
            @click="toggleDescription"
          >
            {{ showFullDescription ? 'Show less' : 'Show more' }}
          </button>
        </p>
      </div>

      <!-- Product Details -->
      <div class="product-details mb-3">
        <!-- Price -->
        <div class="price-info mb-2">
          <span class="price-label">Price:</span>
          <span class="price-value text-primary fw-bold">
            ${{ formattedPrice }}
          </span>
        </div>

        <!-- Stock Information -->
        <div class="stock-info mb-2">
          <span class="stock-label">Stock:</span>
          <span class="stock-value" :class="stockValueClass">
            {{ product.productStock }}
          </span>
          <span class="badge ms-2" :class="statusBadgeClass">
            {{ product.productStockStatus }}
          </span>
        </div>

        <!-- Rating (if from API) -->
        <div v-if="product.rating" class="rating-info mb-2">
          <span class="rating-label">Rating:</span>
          <span class="rating-stars text-warning">
            <i v-for="star in 5" :key="star" 
               :class="star <= Math.round(product.rating.rate) ? 'fas fa-star' : 'far fa-star'">
            </i>
          </span>
          <span class="rating-text text-muted ms-1">
            {{ product.rating.rate }} ({{ product.rating.count }} reviews)
          </span>
        </div>

        <!-- Created/Imported Date -->
        <div class="date-info">
          <small class="text-muted">
            <i class="fas fa-calendar-alt me-1"></i>
            {{ dateLabel }}: {{ formattedDate }}
          </small>
        </div>
      </div>

      <!-- Action Buttons -->
      <div class="mt-auto">
        <div class="d-grid gap-2">
          <button
            @click="editProduct"
            class="btn btn-outline-warning btn-sm"
            :disabled="isDeleting"
          >
            <i class="fas fa-edit me-1"></i>
            Edit Product
          </button>
          <button
            @click="deleteProduct"
            class="btn btn-outline-danger btn-sm"
            :disabled="isDeleting"
          >
            <i class="fas fa-trash-alt me-1"></i>
            {{ isDeleting ? 'Deleting...' : 'Delete Product' }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'

const props = defineProps({ 
  product: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['delete-product', 'edit-product'])

const isDeleting = ref(false)
const showFullDescription = ref(false)

// Computed properties
const formattedPrice = computed(() => 
  Number(props.product.productPrice).toLocaleString('en-US', { 
    minimumFractionDigits: 2,
    maximumFractionDigits: 2 
  })
)

const statusBadgeClass = computed(() => ({
  'bg-success': props.product.productStockStatus === 'In Stock',
  'bg-danger': props.product.productStockStatus === 'Out of Stock',
  'bg-warning': props.product.productStockStatus === 'Low Stock',
  'bg-secondary': !['In Stock', 'Out of Stock', 'Low Stock'].includes(props.product.productStockStatus)
}))

const stockValueClass = computed(() => ({
  'text-success': props.product.productStock > 10,
  'text-warning': props.product.productStock > 0 && props.product.productStock <= 10,
  'text-danger': props.product.productStock === 0
}))

const truncatedDescription = computed(() => {
  if (!props.product.productDescription) return ''
  if (showFullDescription.value || props.product.productDescription.length <= 100) {
    return props.product.productDescription
  }
  return props.product.productDescription.substring(0, 100) + '...'
})

const dateLabel = computed(() => {
  return props.product.source === 'fakestore-api' ? 'Imported' : 'Created'
})

const formattedDate = computed(() => {
  const date = props.product.importedAt || props.product.createdAt
  if (!date) return 'Unknown'
  
  return new Date(date).toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'short',
    day: 'numeric'
  })
})

// Methods
function editProduct() {
  emit('edit-product', props.product)
}

async function deleteProduct() {
  if (!confirm(`Are you sure you want to delete "${props.product.productName}"?`)) return
  
  isDeleting.value = true
  // Simulate loading delay
  await new Promise(resolve => setTimeout(resolve, 500))
  emit('delete-product', props.product.id)
  isDeleting.value = false
}

function toggleDescription() {
  showFullDescription.value = !showFullDescription.value
}

function handleImageError(event) {
  // Replace broken image with placeholder
  event.target.src = 'https://via.placeholder.com/200x150/f8f9fa/6c757d?text=No+Image'
}
</script>

<style scoped>
.product-item-card {
  transition: all 0.3s ease;
  border: 1px solid #e9ecef;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.product-item-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
}

.card-img-wrapper {
  height: 200px;
  overflow: hidden;
  background-color: #f8f9fa;
  display: flex;
  align-items: center;
  justify-content: center;
}

.product-image {
  width: 100%;
  height: 200px;
  object-fit: contain;
  transition: transform 0.3s ease;
}

.product-image:hover {
  transform: scale(1.05);
}

.card-img-placeholder {
  height: 200px;
  background-color: #f8f9fa;
  display: flex;
  align-items: center;
  justify-content: center;
}

.product-name {
  font-size: 1.1rem;
  font-weight: 600;
  color: #2c3e50;
  margin-bottom: 0.5rem;
  line-height: 1.4;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.product-meta {
  margin-bottom: 0.5rem;
}

.product-description {
  flex-grow: 1;
}

.product-description .card-text {
  font-size: 0.9rem;
  line-height: 1.5;
}

.product-details {
  background-color: #f8f9fa;
  padding: 0.75rem;
  border-radius: 0.375rem;
  font-size: 0.9rem;
}

.price-label, .stock-label, .rating-label {
  font-weight: 500;
  color: #6c757d;
}

.price-value {
  font-size: 1.1rem;
  margin-left: 0.5rem;
}

.stock-value {
  margin-left: 0.5rem;
  font-weight: 600;
}

.rating-stars {
  margin-left: 0.5rem;
}

.rating-text {
  font-size: 0.8rem;
}

.date-info {
  border-top: 1px solid #dee2e6;
  padding-top: 0.5rem;
  margin-top: 0.5rem;
}

.btn-sm {
  font-size: 0.875rem;
  padding: 0.375rem 0.75rem;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.badge {
  font-size: 0.75rem;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .product-name {
    font-size: 1rem;
  }
  
  .card-img-wrapper {
    height: 150px;
  }
  
  .product-image {
    height: 150px;
  }
  
  .product-details {
    font-size: 0.85rem;
  }
}
</style>

