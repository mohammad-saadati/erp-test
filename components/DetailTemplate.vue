<template>
  <div v-if="templateData">
    <v-row>
      <v-col
        v-for="(image, i) in templateData"
        :key="i"
        class="d-flex child-flex"
        cols="2"
      >
        <v-img :src="image" aspect-ratio="1" class="grey lighten-2">
          <template v-slot:placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
              <v-progress-circular indeterminate color="grey lighten-5" />
            </v-row>
          </template>
        </v-img>
      </v-col>
    </v-row>
  </div>
</template>
<script>
export default {
  props: {
    // eslint-disable-next-line vue/require-default-prop
    templateData: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      model: 0,
    };
  },
};
</script>
<style lang=""></style>
