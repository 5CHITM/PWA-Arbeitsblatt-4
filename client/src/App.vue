<template>
  <div id="app" class="container d-flex flex-column justify-content-center align-items-center mt-5">
    <img :src="`${serverAddress}/employees.jpg`" class="mx-auto d-block my-3" width="300" alt="picture of employees" />
    <div class="alert alert-danger" role="alert" v-if="offline">Du bist offline...</div>
    <h1>Hey</h1>
    <ButtonGet @get="fetchData"></ButtonGet>
    <CardView :employees="employees" @del="delEmployee"></CardView>
  </div>
</template>

<script>
import axios from 'axios';

import ButtonGet from '@/components/ButtonGet.vue';
import CardView from '@/components/CardView.vue';

export default {
  name: 'app',
  components: {
    ButtonGet,
    CardView,
  },
  data() {
    return {
      serverAddress: process.env.VUE_APP_SERVER,
      employees: [],
      offline: null,
    };
  },
  created() {
    window.addEventListener('online', () => (this.offline = false));
    window.addEventListener('offline', () => (this.offline = true));
    document.addEventListener('swUpdated', this.updateAvailable, { once: true });
  },
  methods: {
    async fetchData() {
      console.log('fetchData called');
      try {
        const { data } = await axios({
          url: `${process.env.VUE_APP_SERVER}/employees`,
          method: 'GET',
        });
        this.employees = data;
      } catch (error) {
        console.error(error);
      }
    },
    async delEmployee(e) {
      console.log('delEmployee called');
      try {
        await axios({
          url: `${process.env.VUE_APP_SERVER}/employees/${e.id}`,
          method: 'delete',
        });
        this.fetchData();
      } catch (error) {
        console.error(error);
      }
    },
    updateAvailable() {
      if (confirm(`New content is available!. Click OK to refresh`)) {
        window.location.reload();
      }
    },
  },
};
</script>

<style></style>
