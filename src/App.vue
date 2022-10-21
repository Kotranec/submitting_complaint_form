<template>
  <v-app>
    <v-main>
      <Complaint/>
    </v-main>
  </v-app>
</template>

<script>
import Complaint from './components/Complaint';

export default {
  name: 'App',

  components: {
    Complaint,
  },

  data: () => ({
    //
  }),
};
</script>
