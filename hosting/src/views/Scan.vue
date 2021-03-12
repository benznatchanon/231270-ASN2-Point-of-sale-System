<template>
  <div class="scan">
    <div class="section">
      <div class="container">
        <form @submit.prevent @keydown.enter.prevent="addItemManually">
          <StreamBarcodeReader
            @decode="addItem"
            class="scanner"
          ></StreamBarcodeReader>

          <div class="columns">
            <div class="column is-half is-full-mobile">
              <ImageBarcodeReader
                class="input"
                @decode="addItem"
                @error="onError"
              ></ImageBarcodeReader>
            </div>
            <div class="column is-half is-full-mobile">
              <b-field>
                <b-input
                  v-model="barcode"
                  type="txet"
                  placeholder="type code"
                  expanded
                />
                <p class="control">
                  <b-button @click.prevent="addItemManually">Add</b-button>
                </p>
              </b-field>
            </div>
          </div>
        </form>
      </div>
    </div>

    <div class="section">
      <div class="container">
        <h1 class="title has-text-right">Total: ฿{{ priceBaht(total) }}</h1>
        <b-table :data="$store.state.basket">
          <div slot="empty"><em>No items in basket</em></div>
          <b-table-column field="barcode" label="Code" v-slot="props">
            {{ props.row.barcode }}
          </b-table-column>
          <b-table-column field="name" label="Product" v-slot="props">
            {{ props.row.name }}
          </b-table-column>
          <b-table-column
            field="priceSatang"
            label="Price"
            numeric
            v-slot="props"
          >
            ฿{{ priceBaht(props.row.priceSatang) }}
          </b-table-column>
          <b-table-column
            field="quantity"
            label="Quantity"
            numeric
            v-slot="props"
          >
            x{{ props.row.quantity }}
          </b-table-column>
          <b-table-column label="Adjust" centered v-slot="props">
            <b-field>
              <b-button
                class="button-left"
                @click.prevent="decItem(props.row.barcode)"
                icon-left="minus"
              ></b-button>
              <b-button
                class="button-right"
                @click.prevent="incItem(props.row.barcode)"
                icon-left="plus"
              ></b-button>
            </b-field>
          </b-table-column>
        </b-table>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component'
import { sum } from 'lodash'
import { StreamBarcodeReader, ImageBarcodeReader } from 'vue-barcode-reader'
import { mapState } from 'vuex'
import { Product } from '@/store/models'

@Component({
  components: {
    StreamBarcodeReader,
    ImageBarcodeReader
  },
  computed: mapState(['basket'])
})
export default class Scan extends Vue {
  basket!: Array<Product & { quantity: number }>
  barcode = ''

  get total (): number {
    return sum(
      this.basket.map(productWithQuantity => productWithQuantity.priceSatang * productWithQuantity.quantity)
    )
  }

  priceBaht (priceSatang: number): string {
    return (priceSatang / 100.0).toLocaleString(undefined, { minimumFractionDigits: 2 })
  }

  addItemManually (): void {
    const barcode = this.barcode
    if (!barcode) return // Ignore empty barcodes
    this.addItem(barcode)
  }

  addItem (barcode: string) {
    this.$store.commit('addItem', barcode)
    this.barcode = ''
  }

  onError () {
    console.log('Uploaded image could not be read')
  }

  incItem (barcode: string): void {
    this.$store.commit('incItem', barcode)
  }

  decItem (barcode: string): void {
    this.$store.commit('decItem', barcode)
  }
}
</script>

<style lang="scss" scoped>
.scanner {
  max-width: 600px;
  margin: 0 auto;
}

.button-left {
  margin-left: auto;
  margin-right: 0.25rem;
}

.button-right {
  margin-left: 0.25rem;
  margin-right: auto;
}
</style>
