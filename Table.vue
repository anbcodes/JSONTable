<template>
  <v-simple-table>
    <thead>
      <th
        v-for="property in [...Object.keys(mainFormat), 'Remove']"
        :key="property"
      >{{property}}</th>
    </thead>
    <tbody>
      <tr
        v-for="(item, i) in Object.keys(data || {})"
        :key="JSON.stringify(item)"
      >
        <td v-for="property in Object.keys(mainFormat)" :key="property">
          <v-text-field
            :value="data[item][property]"
            @input="currentEditing = arguments[0]"
            @click="currentEditing = ''"
            @blur="data[item][property] = currentEditing || data[item][property]"
            v-if="mainFormat[property] === 'Number'"
            type="number"
          />
          <v-text-field
            :value="data[item][property]"
            @input="currentEditing = arguments[0]"
            @click="currentEditing = ''"
            @blur="data[item][property] = currentEditing || data[item][property]"
            v-else-if="mainFormat[property] === 'String'"
          />
          <v-checkbox
            :value="data[item][property]"
            @input="currentEditing = arguments[0]"
            @click="currentEditing = ''"
            @blur="data[item][property] = currentEditing || data[item][property]"
            v-else-if="mainFormat[property] === 'Boolean'"
          />
          <v-dialog v-if="mainFormat[property].split(':')[0] === 'Array'">
            <Table
              :format="format"
              :allData="allData || data"
              :data.sync="data[item][property]"
              :customComponents="customComponents"
              :level="level ? level + 1 : 1"
              :formatPath="formatPart.split(':')[1] + '.' + property"
              pathForData
            />
            <template v-slot:activator="{on}">
              <v-btn v-on="on" icon>
                <v-icon>mdi-pencil</v-icon>
              </v-btn>
            </template>
          </v-dialog>
          <template v-for="custom in customComponents">
            <component
              v-if="mainFormat[property] === custom"
              :is="custom"
              :key="custom"
              :data="data"
              :value="data[item][property]"
              :allData="allData"
              @input="currentEditing = arguments[0]"
              @click="currentEditing = ''"
              @blur="data[item][property] = currentEditing || data[item][property]"
            />
          </template>
        </td>
        <v-btn @click="deleteItem(i)" icon>
          <v-icon>
            mdi-minus
          </v-icon>
        </v-btn>
      </tr>
      <tr class="justify-center">
        <v-btn @click="create()" icon>
          <v-icon>mdi-plus</v-icon>
        </v-btn>
      </tr>
    </tbody>
  </v-simple-table>
</template>
<script>
import dot from 'dot-object';
import Vue from 'vue';

export default {
  name: 'Table',
  props: {
    data: null,
    format: Object,
    formatPath: String,
    customComponents: Array,
    allData: null,
    level: Number,
  },

  components: Vue.JSONSimpleEditor ? Vue.JSONSimpleEditor.components || {} : {},

  computed: {
    formatPart() {
      return dot.pick(this.formatPath, this.format);
    },

    mainFormat() {
      return this.format[this.formatPart.split(':')[1]];
    },
  },

  data: () => ({
    currentEditing: '',
  }),

  methods: {

    create() {
      const newPart = this.createPart();

      const partParent = [...this.data, newPart[0]];
      newPart[0].$parent = partParent;
      this.$emit('update:data', partParent);
      return;
    },

    createPart() {
      const part = [];
      part[0] = {};
      Object.keys(this.mainFormat).forEach((formatPart) => {
        const type = this.mainFormat[formatPart];
        if (this.customComponents.includes(type)) {
          part[0][formatPart] = '';
        } else if (!['String', 'Number', 'Boolean'].includes(type)) {
          part[0][formatPart] = [];
        } else if (type === 'String') {
          part[0][formatPart] = '';
        } else if (type === 'Number') {
          part[0][formatPart] = 0;
        } else if (type === 'Boolean') {
          part[0][formatPart] = false;
        }
        part[0].$parent = this.data;
      });
      return part;
    },

    delete(index) {
      delete this.data[index];
      this.$emit('update:data', this.data.filter(v => v));
    },
  },
};
</script>
