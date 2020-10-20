<template>
  <div class="home">
    <div class="row">
      <div class="col-md-3">
        <v-card class="mx-auto" max-width="344">
          <v-card-title>Filters</v-card-title>
        </v-card>
        <v-card
          class="mx-auto scroll"
          max-width="344"
          max-height="344"
          v-for="(filters, index) in filterCategories"
          :key="index"
        >
          <v-card-actions>
            {{ filters.filter_lable }}
            <v-spacer></v-spacer>

            <v-btn icon @click="toggleShowFilter(index)">
              <v-icon>{{
                showFilter[index] ? "mdi-chevron-up" : "mdi-chevron-down"
              }}</v-icon>
            </v-btn>
          </v-card-actions>

          <v-expand-transition>
            <div v-show="showFilter[index]">
              <v-divider></v-divider>

              <v-list dense>
                <v-list-item-group color="primary">
                  <v-list-item v-for="(options, i) in filters.options" :key="i">
                    <v-list-item-icon>
                      <!-- <v-icon v-text="item.icon"></v-icon> -->
                    </v-list-item-icon>
                    <v-list-item-content>
                      <v-list-item-title
                        v-text="options.value"
                        v-on:click="getFilterOption(options)"
                      ></v-list-item-title>
                    </v-list-item-content>
                  </v-list-item>
                </v-list-item-group>
              </v-list>
            </div>
          </v-expand-transition>
        </v-card>
      </div>
      <div class="col-md-9">
        <v-card class="mx-auto" outlined>
          <v-card-title>{{ productCount }}</v-card-title>
        </v-card>
        <div class="row">
        <div class="col-3" v-for="(product, index) in products" :key="index">
          <v-hover v-slot:default="{ hover }">
          <v-card class="mx-auto" max-width="400" outlined>
            <v-img
              class="white--text align-end"
              height="mx-auto"
              :src="product.image"
            >
            </v-img>

            <v-card-subtitle class="pb-0"> {{product.name}} </v-card-subtitle>

            <v-card-text class="text--primary">
              <div class="row">
                <div class="col-7">
                  Rs.<s> {{product.price }} </s> Rs.{{product.price - ((product.discount / 100) * product.price)}}
                </div>
                <div class="col-3">
                  <div style="color: red">-{{product.discount}}%</div>
                </div>
              </div>
            </v-card-text>
            <v-expand-transition>
                <div
                  v-if="hover"
                  style="height: 100%;"
                >
                  Size- {{product.size}}
                </div>
              </v-expand-transition>
          </v-card>
          </v-hover>
        </div>
      </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

import axios from "axios";

export default {
  name: "Home",
  data() {
    return {
      show: true,
      filterCategories: "",
      products: "",
      productCount: "",
      showFilter: []
    };
  },
  mounted() {
    axios
      .get(
        "https://pim.wforwomanonline.com/pim/pimresponse.php/?service=category&store=1&url_key=top-wear-kurtas&page=1&count=20&sort_by=&sort_dir=desc&filter="
      )
      .then((result) => {
        console.log(result);
        this.filterCategories = result.data.result.filters;
        this.productCount = result.data.result.count;
        this.products = result.data.result.products;
        for(var i=0; i< this.filterCategories.length; i++) {
          this.showFilter.push(true)
        }
        console.log(this.filterCategories.length);
      });
  },
  methods: {
    getFilterOption(value) {
      console.log(value);
      axios.get("https://pim.wforwomanonline.com/pim/pimresponse.php/?service=category&store=1&url_key=top-wear-kurtas&page=1&count=20&sort_by=&sort_dir=desc&filter=" + value.code + "-" +value.value).then((result) => {
        this.productCount = result.data.result.count;
        this.products = result.data.result.products;
        console.log(result);
      })
    },
    toggleShowFilter(value) {
      console.log(this.showFilter[value]);
      this.showFilter[value] = !this.showFilter[value];
      console.log(this.showFilter[value]);
    }
  }
};
</script>

<style scoped>
.scroll {
   overflow-y: scroll
}
</style>