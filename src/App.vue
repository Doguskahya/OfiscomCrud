<template>
  <v-app>
    <v-app-bar justify="space-around" app color="blue-grey-darken-4">
      <v-toolbar-title>Ofiscom Crud</v-toolbar-title>
      <v-btn icon  class="newHead" href='./'> <v-icon> mdi-basket </v-icon> <div> Orders </div> </v-btn>
      <v-btn icon  class="newHead" href='./products'> <v-icon> mdi-folder </v-icon> <div> Products </div> </v-btn>
    </v-app-bar>
    <router-view />
  </v-app>
</template>

<script>
export default {
  name: 'App',
  data () {
    return{

    }
  }
}
</script>
