<template>
  <div class="cart">
    <b-container>
      <b-row>
        <b-col>
          <h2>Products</h2>
        </b-col>
      </b-row>

      <b-row>
        <b-col v-for="product in products" :key="product.id">
          <b-card-group deck>
            <b-card
              :title="product.name"
              :img-src="product.imgUrl"
              img-alt="Image"
              img-top
              tag="article"
              style="max-width: 20rem;"
              class="mb-2 card"
            >
              <b-card-text>Price: {{ product.price }}</b-card-text>
              <b-button
                v-if="!product.cart"
                @click="add(product)"
                href="#"
                variant="primary"
              >
                Add To Shopping Cart
              </b-button>
              <b-button
                v-if="product.cart"
                @click="add(product)"
                :disabled="product.cart"
                variant="warning"
              >
                Product added to Shopping Cart
              </b-button>
            </b-card>
          </b-card-group>
        </b-col>
      </b-row>

      <b-row>
        <b-col>
          <h2>Shopping Cart</h2>
        </b-col>
      </b-row>

      <b-row>
        <b-col>
          <b-table bordered hover :items="cart" :fields="fields">
            <template v-slot:cell(#)="data">
              {{ data.index + 1 }}
            </template>
            <template v-slot:cell(price)="data">
              {{ data.item.price * data.item.quantity }}
            </template>
            <template v-slot:cell(remove)="data">
              <b-button
                @click="remove(data.item.id)"
                variant="danger"
                class="mr-2"
              >
                X
              </b-button>
            </template>
            <template v-slot:cell(quantity)="data">
              <b-row>
                <b-button
                  :disabled="data.item.quantity <= 1"
                  variant="outline-primary"
                  @click="decrement(data.item.id)"
                  class="mr-2"
                >
                  <b-icon aria-hidden="true" icon="dash"></b-icon>
                </b-button>
                <b-col cols="2">
                  <h4>{{ data.item.quantity }}</h4>
                </b-col>
                <b-button
                  variant="outline-primary"
                  @click="increment(data.item.id)"
                >
                  <b-icon aria-hidden="true" icon="plus"></b-icon>
                </b-button>
              </b-row>
            </template>

            <template v-slot:cell(image)="data">
              <b-img
                style="max-width: 5rem"
                width="64"
                :src="data.item.imgUrl"
                fluid
              ></b-img>
            </template>
          </b-table>
        </b-col>
      </b-row>

      <!-- <b-row v-if="cart.length > 0">
        <b-col></b-col>
        <b-col>
          <h2>Total: $ {{ total }}.00</h2>
        </b-col>
        <b-col></b-col>
        <b-col></b-col>
      </b-row> -->

      <b-row v-if="cart.length > 0">
        <b-col>
          <b-button @click="clean" variant="info" block class="mr-2">
            Clean
          </b-button>
        </b-col>
        <b-col></b-col>
        <b-col></b-col>
        <b-col></b-col>
        <b-col>
          <b-button @click="buy" variant="success" block class="mr-2">
            <b-badge variant="light">{{ quantityCounter }}</b-badge>
            Buy <b-icon icon="credit-card"></b-icon><br />
            Total: $ {{ total }}.00
          </b-button>
        </b-col>
      </b-row>

      <b-modal
        hide-header-close
        no-close-on-esc
        no-close-on-backdrop
        ref="modal-1"
        centered
        title="Purchase Completed"
      >
        <template slot="modal-footer">
          <b-button @click="clean" variant="info" block class="mt-3">
            Close
          </b-button>
        </template>

        <p class="my-4">Products:</p>
        <ul v-for="productFinal in ticket.product" :key="productFinal.id">
          <li>Product name: {{ productFinal.name }}</li>
          <li>Quantity: {{ productFinal.quantity }}</li>
          <li>Price: {{ productFinal.price }}</li>
          <li>Total: {{ productFinal.price * productFinal.quantity }}</li>
          <hr />
        </ul>
        <h2 class="my-4">Total: ${{ ticket.total }}.00</h2>
      </b-modal>
    </b-container>
  </div>
</template>

<script>
import productsList from "../assets/codebeautify.json";

export default {
  name: "Cart",
  data() {
    return {
      products: productsList.arrayOfProducts,
      ticket: {
        product: null,
        total: 0
      },
      counter: 0,
      cart: [],
      fields: ["#", "remove", "image", "name", "quantity", "price"]
    };
  },
  methods: {
    add(product) {
      this.products[product.id - 1].cart = true;
      this.cart.push(product);
      this.counter++;
    },
    clean() {
      this.cart = [];
      for (const key in this.products) {
        this.products[key].cart = false;
        this.products[key].quantity = 1;
      }

      this.$refs["modal-1"].hide();
    },
    remove(id) {
      for (let index = 0; index < this.products.length; index++) {
        if (this.products[index].id == id) {
          this.products[index].cart = false;
          this.products[index].quantity = 1;
        }
      }
      for (let index = 0; index < this.cart.length; index++) {
        if (this.cart[index].id == id) {
          this.cart.splice(index, 1);
        }
      }
    },
    buy() {
      this.ticket = {
        product: this.cart,
        total: this.total
      };
      this.$refs["modal-1"].show();
    },
    increment(id) {
      for (let index = 0; index < this.cart.length; index++) {
        if (this.cart[index].id == id) {
          this.cart[index].quantity++;
        }
      }
    },
    decrement(id) {
      for (let index = 0; index < this.cart.length; index++) {
        if (this.cart[index].id == id) {
          this.cart[index].quantity--;
        }
      }
    }
  },
  computed: {
    total() {
      let totalPrice = 0;

      for (let index = 0; index < this.cart.length; index++) {
        totalPrice += this.cart[index].quantity * this.cart[index].price;
      }
      return totalPrice;
    },
    quantityCounter() {
      let totalQuantity = 0;

      for (let index = 0; index < this.cart.length; index++) {
        totalQuantity += this.cart[index].quantity;
      }
      return totalQuantity;
    }
  }
};
</script>

<style scoped>
.card {
  height: 500px;
  box-sizing: border-box;
}
</style>
