<template>
    <div class="container">
        <h2 class="my-4">Additional API Data</h2>
        <div class="row">
            <div
                v-for="product in products"
                :key="product.id"
                class="col-12 col-sm-6 col-md-4 col-lg-3 mb-4"
            >
                <div class="card h-100">
                    <img
                        :src="product.image"
                        :alt="product.title"
                        class="card-img-top"
                        style="object-fit: contain; height: 120px;"
                    />
                    <div class="card-body p-2">
                        <h6 class="card-title text-truncate" :title="product.title">{{ product.title }}</h6>
                        <p class="card-text small text-truncate" :title="product.description">{{ product.description }}</p>
                    </div>
                    <div class="card-footer d-flex flex-column align-items-start p-2">
                        <span class="mb-1"><strong>${{ product.price }}</strong></span>
                        <span class="text-muted small">Rating: {{ product.rating.rate }} ({{ product.rating.count }})</span>
                        <button class="btn btn-primary btn-sm mt-2 w-100">Buy Now</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "FetchProduct",
    data() {
        return { products: [] };
    },
    async mounted() {
        try {
            const res = await fetch("https://fakestoreapi.com/products");
            this.products = await res.json();
        } catch (error) {
            console.error("Fetch error:", error);
        }
    },
};
</script>
