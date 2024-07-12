<template>
  <div class="container">
    <div class="price-range-buttons">
      <button
        v-for="priceRange in priceRanges"
        :key="priceRange"
        @click="filterProductsByPriceRange(priceRange)"
      >
        {{ priceRange }}
      </button>
    </div>
    <h2>{{ selectedPriceRange }} 상품</h2>
    <ul class="list-group">
      <li
        v-for="product in filteredProducts"
        :key="product.id"
        class="list-group-item"
      >
        {{ product.name }} - {{ product.price | formatPrice }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: '상품1', price: 10000 },
        { id: 2, name: '상품2', price: 20000 },
        { id: 3, name: '상품3', price: 30000 },
        { id: 4, name: '상품4', price: 40000 },
        { id: 5, name: '상품5', price: 50000 },
      ],
      priceRanges: [
        '10000원 ~ 20000원',
        '20000원 ~ 30000원',
        '30000원 ~ 40000원',
        '40000원 ~ 50000원',
      ],
      selectedPriceRange: '전체 상품',
      filteredProducts: this.products,
    }
  },
  methods: {
    filterProductsByPriceRange(priceRange) {
      this.selectedPriceRange = priceRange
      this.filteredProducts = this.products.filter((product) => {
        const minPrice = priceRange.split(' ~ ')[0].replace(/원/, '')
        const maxPrice = priceRange.split(' ~ ')[1].replace(/원/, '')
        return product.price >= minPrice && product.price <= maxPrice
      })
    },
  },
}
</script>
