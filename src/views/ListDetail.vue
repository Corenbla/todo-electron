<template>
  <div>
    <div>{{ list.title }} - Nb items: {{ list.nb_item }}</div>
    <ul>
      <li v-for="item in list.items" :key="item.id" v-bind:class="{ crossedOut: item.done }">
        <input
          type="checkbox"
          :id="'checkbox' + item.id"
          v-model="item.done"
          v-on:change="checkItem(item.id)"
        />{{ item.title }}
      </li>
    </ul>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios';

const { ipcRenderer } = window.require('electron');

export default {
  name: 'List',
  data() {
    return { list: {} };
  },
  async beforeCreate() {
    const { restHost } = await ipcRenderer.invoke('getStoreValue', 'conf');

    const getList = await axios({
      method: 'GET',
      url: `${restHost}list/${this.$route.params.listId}`,
    });

    this.list = getList.data;

    console.log(this.list);
  },
  methods: {
    async checkItem(id) {
      const { restHost } = await ipcRenderer.invoke('getStoreValue', 'conf');

      await axios({
        method: 'PUT',
        url: `${restHost}/item/${id}/check`,
      });
    },
  },
};
</script>

<style>
.crossedOut {
  text-decoration: line-through;
}
</style>
