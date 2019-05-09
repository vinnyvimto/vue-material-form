<template>
  <div>
    <v-expansion-panel expand :value="alwaysOpen">
      <v-expansion-panel-content v-for="(item, i) in orders" :key="i">
        <template v-slot:header>
          <div>
            <span v-if="item.hasOwnProperty('title')" v-text="item.title"></span>
            <span v-else>Order {{ i + 1 }}</span>
            <span v-if="item.hasOwnProperty('total')">
              - £{{ item.total }}
              <v-badge class="pl-3 v-badge--inline" :color="item.service.color">
                <template v-slot:badge>
                  <span v-text="item.service.id"></span>
                </template>
              </v-badge>
            </span>
          </div>
        </template>
        <template v-slot:actions>
          <v-btn v-if="orders.length > 1" flat icon color="red" @click.stop="removeOrder(i)">
            <v-icon light small>$vuetify.icons.close</v-icon>
          </v-btn>
          <v-btn flat icon color="transparent">
            <v-icon>$vuetify.icons.expand</v-icon>
          </v-btn>
        </template>
        <v-card>
          <v-card-text>
            <ServiceForm :edit-data="item" @confirmed="addOrder" />
          </v-card-text>
        </v-card>
      </v-expansion-panel-content>
    </v-expansion-panel>

    <v-checkbox
      v-model="hasAgreed"
      :rules="[v => !!v || 'You must agree to continue!']"
      label="Do you agree?"
      required
    ></v-checkbox>

    <v-btn color="success" :disabled="!hasAgreed" @click="continued">
      Continue
    </v-btn>

    <v-btn @click="createOrder" :disabled="!completedLastOrder">
      Add Order
    </v-btn>
  </div>
</template>

<script>
import ServiceForm from "./ServiceForm.vue";

export default {
  name: "ServiceList",

  components: {
    ServiceForm
  },

  data() {
    return {
      orders: [{}],
      alwaysOpen: 0,
      hasAgreed: false
    };
  },

  computed: {
    completedLastOrder() {
      const lastItem = this.orders[this.orders.length - 1];
      return Object.entries(lastItem).length !== 0;
    }
  },

  methods: {
    addOrder(order) {
      const index = this.orders.findIndex(f => f.id === order.id);
      if (index > -1) {
        this.orders.splice(index, 1, order);
      } else {
        this.orders = [...this.orders.slice(0, -1), order];
      }

      this.alwaysOpen = [];
    },

    createOrder() {
      this.orders.push({});
      this.alwaysOpen = this.orders.length - 1;
    },

    continued() {
      this.$emit("continued", this.orders);
    },

    removeOrder(index) {
      if (confirm("Are you sure you wish to remove this order?")) {
        this.orders.splice(index, 1);
      }
    }
  }
};
</script>

<style lang="scss">
.v-badge--inline {
  top: -5px;
}
</style>
