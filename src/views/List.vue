<template>
  <ul>
    <li v-for="list in lists" :key="list.id">
      <router-link :to="{ name: 'ListDetail', params: { listId: list.id } }">{{
        list.title
      }}</router-link>
      - Nb items: {{ list.nb_item }} - <span v-on:click="delList(list.id)">Del</span> -
      <span>Edit</span>
    </li>
  </ul>
</template>

<script>
// @ is an alias to /src
import axios from 'axios';

const { ipcRenderer } = window.require('electron');

export default {
  name: 'List',
  data() {
    return { lists: [] };
  },
  async beforeCreate() {
    const { restHost } = await ipcRenderer.invoke('getStoreValue', 'conf');

    const getList = await axios({ method: 'GET', url: `${restHost}list` });

    this.lists = getList.data;

    await ipcRenderer.invoke('setStoreValue', 'test', 'toto');

    const toto = await ipcRenderer.invoke('getStoreValue', 'test');
    console.log(toto);
  },
  methods: {
    async delList(id) {
      const { restHost } = await ipcRenderer.invoke('getStoreValue', 'conf');

      await axios({
        method: 'DELETE',
        url: `${restHost}/list/${id}`,
      });

      await this.fetchLists();
    },
    async fetchLists() {
      const { restHost } = await ipcRenderer.invoke('getStoreValue', 'conf');

      const getList = await axios({ method: 'GET', url: `${restHost}list` });

      this.lists = getList.data;
    },
  },
};
</script>
