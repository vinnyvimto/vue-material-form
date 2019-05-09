<template>
  <v-form ref="form" v-model="isValid" lazy-validation>
    <v-text-field
      v-model="title"
      :counter="15"
      :rules="titleRules"
      label="Title"
      required
    ></v-text-field>

    <v-select
      v-model="selected"
      :items="services"
      :rules="[v => !!v || 'Service is required']"
      label="Service"
      required
    >
      <template v-slot:item="{ item }">
        <span
          >{{ item.text }}
          <v-badge class="pl-3 v-badge--inline" :color="item.color">
            <template v-slot:badge>
              <span v-text="item.id"></span>
            </template> </v-badge
        ></span>
      </template>
      <template v-slot:selection="{ item }">
        <span
          >{{ item.text }}
          <v-badge class="pl-3 v-badge--inline" :color="item.color">
            <template v-slot:badge>
              <span v-text="item.id"></span>
            </template> </v-badge
        ></span>
      </template>
    </v-select>

    <v-layout row>
      <v-flex shrink style="width: 100px">
        <v-text-field v-model="quantity" hide-details type="number" label="Quantity"></v-text-field>
      </v-flex>
      <v-flex class="pl-3">
        <v-slider
          v-model="quantity"
          :min="qtyRange[0]"
          :max="qtyRange[qtyRange.length - 1]"
        ></v-slider>
      </v-flex>
    </v-layout>

    <v-text-field label="Unit Cost" :value="unitCost" prefix="£" disabled></v-text-field>

    <v-text-field label="Total Cost" :value="total" prefix="£" disabled></v-text-field>

    <v-btn :disabled="!isValid" @click="validate">
      Confirm
    </v-btn>
  </v-form>
</template>

<script>
export default {
  name: "ServiceForm",

  props: {
    editData: {
      type: Object,
      default: function() {
        return {};
      }
    }
  },

  data() {
    return {
      isValid: true,
      title: "",
      titleRules: [
        v => !!v || "Title is required",
        v => (v && v.length <= 15) || "Title must be less than 15 characters"
      ],
      services: [
        { id: 1, text: "Service 1", cost: 10.0, color: "purple" },
        { id: 2, text: "Service 2", cost: 14.99, color: "teal" },
        { id: 3, text: "Service 3", cost: 20.0, color: "amber" }
      ],
      selected: "Service 1",
      min: 1,
      max: 100,
      quantity: 1,
      qtyRange: [1, 100]
    };
  },

  computed: {
    unitCost() {
      return this.services.find(s => s.text === this.selected).cost.toFixed(2);
    },
    total() {
      return (this.quantity * this.unitCost).toFixed(2);
    }
  },

  methods: {
    validate() {
      if (this.$refs.form.validate()) {
        const uuid = this.editData.id || this.generateOrderUuid();

        const data = {
          id: uuid,
          title: this.title,
          service: this.services.find(s => s.text === this.selected),
          quantity: this.quantity,
          total: this.total
        };

        this.$emit("confirmed", data);
      }
    },
    generateOrderUuid() {
      return Math.random()
        .toString(36)
        .substring(7);
    }
  }
};
</script>
