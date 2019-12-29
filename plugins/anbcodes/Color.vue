<template>
  <v-autocomplete
    @blur="$emit('blur')"
    @click="$emit('click')"
    :items="colors"
    v-model="color"
    :color="color"
  />
</template>
<script>
export default {
  props: {
    data: null,
    value: String,
  },

  data: () => ({
    colors: ['red', 'pink', 'purple', 'deep-purple', 'indigo', 'blue', 'light-blue', 'cyan', 'teal', 'green', 'light-green', 'lime', 'yellow', 'amber', 'orange', 'deep-orange', 'brown', 'blue-grey', 'grey'],
    color: '',
  }),

  watch: {
    value() {
      this.color = this.value;
    },

    color() {
      this.$emit('input', this.color);
    },
  },
};
</script>
