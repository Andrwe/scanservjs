<template>
  <v-simple-table>
    <thead>
      <tr>
        <th>Filename</th>
        <th class="file-date">Date</th>
        <th>Size</th>
        <th></th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="file in files" v-bind:key="file.name">
        <td><a @click="open(file)">{{ file.name }}</a></td>
        <td class="file-date">{{ file.lastModified }}</td>
        <td>{{ file.sizeString }}</td>
        <td><v-btn color="secondary" v-on:click="fileRemove(file)" icon><v-icon>mdi-delete</v-icon></v-btn></td>
      </tr>
    </tbody>
  </v-simple-table> 
</template>

<script>
import Common from '../classes/common';

export default {
  name: 'Files',

  mounted() {
    this.fileList();
  },

  data() {
    return {
      files: []
    };
  },

  methods: {
    fileList() {
      this.$emit('mask', 1);
      Common.fetch('files').then(files => {
        this.files = files;
        this.$emit('mask', -1);
      }).catch(error => {
        this.$emit('notify', { type: 'e', message: error });
        this.$emit('mask', -1);
      });
    },

    fileRemove(file) {
      this.$emit('mask', 1);
      Common.fetch('files/' + encodeURIComponent(file.fullname), {
        method: 'DELETE'
      }).then(data => {
        this.$emit('notify', { type: 'i', message: `Deleted ${data.name}` });
        this.fileList();
        this.$emit('mask', -1);
      }).catch(error => {
        this.$emit('notify', { type: 'e', message: error });
        this.$emit('mask', -1);
      });
    },

    open(file) {
      window.location.href = 'files/' + encodeURIComponent(file.fullname);
    }
  }
};
</script>

<style>

</style>