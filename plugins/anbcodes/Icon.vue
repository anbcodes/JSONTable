<template>
  <v-autocomplete
    @blur="$emit('blur')"
    @click="$emit('click')"
    :items="icons"
    v-model="icon"
    :prepend-icon="'mdi-' + icon.text"
    :filter="filter"
    return-object
  >
    <template v-slot:item="data">
      <v-list-item-icon>
        <v-icon>mdi-{{data.item.name}}</v-icon>
      </v-list-item-icon>
      <v-list-item-content>{{data.item.name}}</v-list-item-content>
    </template>
  </v-autocomplete>
</template>
<script>
import icons from './meta';

export default {
  props: {
    data: null,
    value: String,
  },

  data: () => ({
    icons: [],
    icon: {},
  }),

  watch: {
    value() {
      this.icon.text = this.value;
    },

    icon() {
      this.$emit('input', this.icon.text);
    },
  },

  created() {
    this.loadIcons();
  },
  methods: {
    filter(item, query) {
      const name = item.name.toLowerCase();
      const aliases = item.aliases.toLowerCase();
      const tags = item.tags.toLowerCase();
      const searchText = query.toLowerCase();

      return name.indexOf(searchText) > -1
          || aliases.indexOf(searchText) > -1
          || tags.indexOf(searchText) > -1;
    },
    loadIcons() {
      const newIcons = icons.map((icon) => {
        const newIcon = {};
        newIcon.aliases = icon.aliases.join(' ');
        newIcon.tags = icon.tags.join(' ');
        newIcon.name = icon.name;
        newIcon.value = icon.name;
        newIcon.text = icon.name;
        return newIcon;
      });
      this.icons = newIcons;
    },
  },
};
</script>
