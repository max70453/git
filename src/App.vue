<template>
  <v-app>
    <v-main>
      <Front/>
    </v-main>
  </v-app>
</template>

<script>
import Front from './components/Front';

export default {
  name: 'App',

  components: {
    Front,
  },

  data: () => ({
    //
  }),
};
</script>
