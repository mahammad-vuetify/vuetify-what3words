<template>
  <v-autocomplete
    v-model="select"
    :loading="loading"
    :items="items"
    item-text="nearestPlace"
    item-value="words"
    :search-input.sync="search"
    cache-items
    class="mx-4"
    flat
    hide-no-data
    hide-details
    label="film.crunchy.spiri"
    solo-inverted
    @change="onChange()"
  ></v-autocomplete>
</template>

<script>
export defaault {
  data() {
    return {
      loading: false,
      items: [],
      search: null,
      select: null,
      result: null
    };
  },
  watch: {
    search(val) {
      val && val !== this.select && this.querySelections(val);
    }
  },
  methods: {
    async querySelections(input) {
      this.loading = true;
      const response = await axios.get(
        "https://api.what3words.com/v3/autosuggest",
        {
          params: {
            key: "QNQCX209",
            "clip-to-country": "Gb",
            input
          }
        }
      );

      if (response) {
        this.items = response.data.suggestions;
      }

      this.loading = false;
    },
    async onChange() {
      if (this.select) {
        const response = await axios.get(
          `https://api.what3words.com/v3/convert-to-coordinates?`,
          {
            params: {
              key: "QNQCX209",
              words: this.select
            }
          }
        );

        if (response) {
          this.result = response.data;
          this.$emit("change", this.result);
        }
      }
    }
  }
}
</script>
