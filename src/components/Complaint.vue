<template>
  <div class="container">   
    <div class="conteiner_header"> 
      <Header/>     
    </div>
    <Content/>     
  </div>
  
</template>

<script>
  import Header from "./Header.vue";
  import Content from "./Content.vue";

  export default {
    name: 'Complaint',
    components: {
      Header,
      Content,
    },

    data: () => ({
      //
    }),
  }
</script>

<style scoped>
.container {
  max-width: 1413px;
  min-width: 700px;
  margin: 0 auto;
}

.conteiner_header {
  height: 64px;
}
</style>
