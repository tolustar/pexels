<template>
  <div class="home">
   
   <div>
      <v-banner dark :sticky="true" :single-line="true" elevation="12" v-if="show_pwa_prompt == true">
        
        Do you want to install this app on your device

        <template v-slot:actions>
          <button type="button" name="" id="" class="btn btn-success" @click="install_pwa()">Yes</button>
          <button type="button" name="" id="" class="btn btn-danger" @click="show_pwa_prompt = false">No</button>
        </template>

      </v-banner>
    </div>

    <div class="d-flex flex-column align-items-center justify-content-center home_banner" style="height: 80vh">
      <div>
        <img src="./../assets/logo.png" />
      </div>
      <h1 class="text-center text-white mt-3">Pexels</h1>
      <h5 class="text-center text-white mt-2">
        The best free stock photos & videos shared by talented creators.
      </h5>

      <div class="form-group" style="width: 70vw">
        <label for=""></label>
        <input type="text" class="form-control" style="height: 60px;" aria-describedby="helpId" placeholder="Search for free photos and videos"/>
        <p id="helpId" class="form-text text-muted">
          Suggested: smile, love, happiness
        </p>
      </div>

      <div>
        <button type="button" name="" id="" class="btn bg-white btn-lg btn-block mt-2">
          Search
        </button>
      </div>
    </div>

    

  </div>
</template>

<style scoped>
.home_banner {
  background-image: url("./../assets/home_banner.jpg");
  background-repeat: no-repeat;
  background-position: cover;
}
</style>

<script>
import pwaInstallHandler from 'pwa-install-handler'


export default {
  name: "home",
  components: {},
  data() {
    return {
      show_pwa_prompt: false,
      message: '',
    };
  },
  created() {
    this.check_if_device_can_install_pwa();
  },
  computed: {},
  methods: {
    check_if_device_can_install_pwa(){
      
      pwaInstallHandler.addListener((canInstall) => {
        this.show_pwa_prompt = canInstall;
      })
      
    },
    
    install_pwa(){
      pwaInstallHandler.install().then((isInstalled) => {  
        console.log(
            isInstalled
                ? 'User accepted installation prompt'
                : 'User rejected installation prompt'
        )
      });
    
      this.show_pwa_prompt = false;
    }
  }
};
</script>
