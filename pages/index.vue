<template>
  <div>
    <v-row align="center">
      <v-col class="d-flex" cols="4">
        <v-select
          v-model="category"
          :items="categories"
          label="product category"
          solo
          clearable
        ></v-select>
      </v-col>
    </v-row>
    <DxDataGrid
      ref="dataGridRefKey"
      :data-source="productsData"
      key-expr="id"
      :show-column-lines="showColumnLines"
      :show-row-lines="showRowLines"
      :show-borders="showBorders"
      :row-alternation-enabled="rowAlternationEnabled"
      :repaint-changes-only="true"
      :allow-column-reordering="true"
    >
      <DxColumnChooser :enabled="true" />
      <DxEditing
        refresh-mode="reshape"
        :allow-adding="true"
        :allow-updating="true"
        :allow-deleting="true"
        new-row-position="viewportTop"
        mode="cell"
      />
      <DxFilterRow :visible="true" />
      <DxColumn data-field="title" />
      <DxColumn data-field="brand" />
      <DxColumn data-field="category" />
      <DxColumn data-field="price" />
      <DxColumn data-field="discountPercentage" />
      <DxColumn data-field="rating" />
      <DxColumn data-field="stock" />
      <DxColumn data-field="description" />
      <DxColumn
        :height="70"
        :allow-sorting="false"
        data-field="thumbnail"
        cell-template="cellTemplate"
      />
      <DxMasterDetail :enabled="true" template="masterDetailTemplate" />
      <DxScrolling row-rendering-mode="standard" />
      <DxPaging :page-size="10" />
      <DxPager
        :visible="true"
        :allowed-page-sizes="[5, 10, 'all']"
        :show-page-size-selector="true"
        :show-info="true"
        :show-navigation-buttons="true"
      />
      <template #cellTemplate="{ data }">
        <v-img
          lazy-src="https://picsum.photos/id/11/10/6"
          max-height="70"
          max-width="150"
          :src="data.value"
        />
      </template>
      <template #masterDetailTemplate="{ data: data }">
        <DetailTemplate :template-data="data.data.images" />
      </template>
    </DxDataGrid>
  </div>
</template>
<script>
import {
  DxDataGrid,
  DxColumn,
  DxPager,
  DxPaging,
  DxScrolling,
  DxFilterRow,
  DxMasterDetail,
  DxEditing,
  DxColumnChooser,
} from "devextreme-vue/data-grid";
import CustomStore from "devextreme/data/custom_store";
import DetailTemplate from "@/components/DetailTemplate";

export default {
  components: {
    DxDataGrid,
    DxColumn,
    DxPager,
    DxPaging,
    DxFilterRow,
    DetailTemplate,
    DxMasterDetail,
    DxEditing,
    DxColumnChooser,
    DxScrolling,
  },
  data() {
    return {
      productsData: new CustomStore({
        key: "id",
        load: () =>
          this.sendRequest(
            `${
              this.category
                ? "/products/category/" + this.category
                : "/products"
            }`
          ),
        insert: (values) => this.sendRequest(`/products/add`, "POST", values),
        update: (key, values) =>
          this.sendRequest(`products/${key}`, "PUT", values),
        remove: (key) =>
          this.sendRequest(`products/${key}`, "DELETE", {
            key,
          }),
      }),
      showColumnLines: false,
      showRowLines: true,
      showBorders: true,
      rowAlternationEnabled: true,
      categories: [],
      category: null,
    };
  },
  async created() {
    try {
      const res = await this.$axios.$get("/products/categories");
      this.categories = res;
    } catch (error) {}
  },
  methods: {
    sendRequest(url, method = "GET", data = {}) {
      try {
        switch (method) {
          case "GET":
            return this.$axios
              .$get(url)
              .then((data) => ({
                data: data.products,
                total: data.total,
                skip: data.skip,
                limit: data.limit,
              }))
              .catch(() => {
                throw new Error("Data Loading Error");
              });
          case "POST":
            return this.$axios
              .$post(url, { ...data })
              .then((data) => {
                return data;
              })
              .catch(() => {
                throw new Error("Data Loading Error");
              });
          case "PUT":
            return this.$axios
              .$put(url, { ...data })
              .then((data) => {
                return data;
              })
              .catch(() => {
                throw new Error("Data Loading Error");
              });
          case "DELETE":
            return this.$axios
              .$delete(url)
              .then((data) => {
                return data;
              })
              .catch(() => {
                throw new Error("Data Loading Error");
              });
        }
      } catch (error) {
        return error;
      }
    },
  },
  watch: {
    async category() {
      await this.productsData.load();
      await this.$refs.dataGridRefKey.instance.refresh();
    },
  },
};
</script>
