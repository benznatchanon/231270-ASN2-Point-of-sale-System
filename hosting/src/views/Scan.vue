<template>
  <div>
    <form @submit.prevent @keydown.enter.prevent="addItem">
      <fieldset>
        <legend>Scan</legend>
        <input v-model="barcode" />
        <button @click="addItem">Add</button>
      </fieldset>
    </form>
  </div>
  <div>
    <h1>Total: B{{ priceBaht(total) }}</h1>
    <table>
      <tr>
        <th>Code</th>
        <th>Product</th>
        <th>Price</th>
        <th>Quantity</th>
      </tr>
      <tr v-for="(item, idx) in lineItems" :key="item.barcode">
        <td>{{ item.barcode }}</td>
        <td>{{ item.name }}</td>
        <td>B{{ priceBaht(item.priceSatang) }}</td>
        <td>{{ item.quantity }}</td>
        <td>
          <button @click.prevent="decItem(idx)">-</button>
          <button @click.prevent="incItem(idx)">+</button>
        </td>
      </tr>
    </table>
  </div>
</template>

<script lang="ts">
import { findProductById, Product } from '@/_services/fake-db'
import { sum } from 'lodash'
import { Vue } from 'vue-class-component'

export default class Home extends Vue {
  barcode = ''
  lineItems: Array<Product & { quantity: number }> = []

  get total (): number {
    return sum(this.lineItems.map(item => item.priceSatang * item.quantity))
  }

  priceBaht (priceSatang: number): string {
    return (priceSatang / 100.0).toLocaleString(undefined, { minimumFractionDigits: 2 })
  }

  addItem (): void {
    // Ignore empty barcodes
    if (!this.barcode) return

    // Create product line
    const product = findProductById(this.barcode)
    const productWithQuantity = Object.assign(product, { quantity: 1 })
    this.lineItems.unshift(productWithQuantity)

    // Clear barcode
    this.barcode = ''
  }

  incItem (index: number): void {
    // Ignore invalid indexes
    if (index < 0 || index >= this.lineItems.length) return

    // Increment quantity
    const item = this.lineItems[index]
    item.quantity += 1
  }

  decItem (index: number): void {
    // Ignore invalid indexes
    if (index < 0 || index >= this.lineItems.length) return

    // Decrement quantity
    const item = this.lineItems[index]
    item.quantity -= 1

    // Remove if 0
    if (item.quantity <= 0) this.lineItems.splice(index, 1)
  }
}
</script>

<style lang="scss" scoped>
div {
  margin-bottom: 2rem;
}

form {
  display: inline-block;
}

table {
  margin: 0 auto;
  border-collapse: separate;
  border-spacing: 0.4rem 0.4rem;
}

td {
  padding: 1.2rem 2rem;
  background-color: #f5f5f5;
}

td button {
  width: 1.75rem;
  height: 1.75rem;
}

td button:not(:last-child) {
  margin-right: 0.5rem;
}
</style>
