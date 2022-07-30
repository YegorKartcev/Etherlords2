<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Редактор сохранений"/>
    <div class="row justify-content-center">
      <div class="col-12">
        <SingleFileUploadComponent/>
      </div>
    </div>
    
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import SingleFileUploadComponent from '@/components/SingleFileUpload.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld,
    SingleFileUploadComponent
  },
  methods: {

  },
}
</script>
