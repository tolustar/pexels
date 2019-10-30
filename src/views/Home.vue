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
        <input type="text" class="form-control" style="height: 60px;" aria-describedby="helpId" placeholder="Search for free photos and videos" v-model="search_param"/>
        <p id="helpId" class="form-text text-muted">
          Suggested: smile, love, happiness
        </p>
      </div>

      <div>
        <button type="button" name="" id="" class="btn bg-white btn-lg btn-block mt-2" :to="{name:results, params:{search: search_param}}">
          Search
        </button>
      </div>
    </div>


    <div>
      <h2 class="text-center mt-5">Trending Photos</h2>
      <div class="container">
          <div class="row">
            <div class="col-md-3" v-for="photo in photos" :key="photo.id">
                <div class="mb-4 pa-2">
                  <img :src="photo.src.medium" alt="" class="trending_photos" />
                  <div class="card photographer">
                    <small class="card-title text-white"><v-icon color="#fff">mdi-camera</v-icon> {{photo.photographer}}</small>
                  </div>
                </div>
            </div>
          </div>
      </div>
      <div class="d-flex flex-row justify-content-center">
          <v-btn color="#237e71" class="mb-5 mt-2 text-white" v-if="photos.length != 0" @click="load_trending_photos()" :loading="loading_photos" :disabled="loading_photos">Load More</v-btn>
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

.trending_photos{
  width: 100%;
  height: 250px;
  object-fit: cover;
}

.photographer{
  background-color: #237e71;
  padding-top: 8px;
}
</style>

<script>
import pwaInstallHandler from 'pwa-install-handler'
import axios from 'axios'

export default {
  name: "home",
  components: {},
  data() {
    return {
      show_pwa_prompt: false,
      photos: [],
      photo_page: 1,
      loading_photos: false,
      search_param: '',
    };
  },
  created() {
    this.check_if_device_can_install_pwa();
    this.load_trending_photos();
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
    },

    load_trending_photos(){
      this.loading_photos = true;

      axios
      .get("https://api.pexels.com/v1/popular?per_page=20&page=" + this.photo_page,{ 
          params: {},
          headers: {
            "Authorization": "563492ad6f91700001000001daceeaf5b0f84a8299f1817f13f87e13"
          } 
      })
      .then(response => {
        this.photos = this.photos.concat(response.data.photos);
        this.photo_page = this.photo_page + 1;
        this.loading_photos = false;

        console.log("response", response);
      })
      .catch(()=>{
        this.loading_photos = false;
      });
    }
  }
};
</script>
