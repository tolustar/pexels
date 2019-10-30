<template>
  <div>

        <div class="d-flex flex=row">
            
        </div>

        <div class="container-fluid bg-dark">
            <div class="row">
                <div class="col-md-4">
                    <router-link :to="{name: 'home'}">
                    <div class="text-left pl-5">
                        <img src="./../assets/logo.png" height="40px" />
                        <span class="text-white ml-3">Pexels</span>
                    </div>
                    </router-link>
                </div>
                <div class="col-md-6">
                    <div class="input-group pr-5">
                        <input type="text" class="form-control" v-model="search_param" placeholder="Search for free photos and videos" aria-label="Search for free photos and videos" aria-describedby="button-search">
                        <div class="input-group-append">
                            <button class="btn btn-secondary" type="button" id="button-search" @click="search()">Search</button>
                        </div>
                    </div>
                </div>
                <div class="col-md-2"></div>
            </div>
        </div>

        <div class="text-center mt-4">
            <h2>{{search_param}}</h2>
        </div>

        <div class="container mt-4">
            <div class="row">
                <div class="col-md-3" v-for="photo in photos" :key="photo.id">
                    <div class="mb-4 pa-2" @click="select_photo(photo)" style="cursor: pointer">
                    <img :src="photo.src.medium" alt="" class="search_photos" />
                    <div class="card photographer">
                        <small class="card-title text-white"><v-icon color="#fff">mdi-camera</v-icon> {{photo.photographer}}</small>
                    </div>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="d-flex flex-row justify-content-center">
            <v-btn v-if="photos.length != 0" color="#237e71" class="mb-5 mt-2 text-white" @click="load_photos('previous')" :disabled="photo_page == 1">Previous</v-btn> 
            
            <v-btn class="mb-5 mt-2 mx-3" v-if="check_if_search_is_empty != true" outlined :loading="loading_photos">{{photo_page}}</v-btn> 
            
            <v-btn v-if="photos.length != 0" color="#237e71" class="mb-5 mt-2 text-white" @click="load_photos('next')">Next</v-btn>
        </div>

        <div class="text-center" v-if="check_if_search_is_empty == true">
            <h2>Sorry we could not find pictures that match your query, please try another word</h2>
        </div>


        <v-dialog v-model="dialog" width="80%">
        

            <v-card>
                        
                <v-card-text>
                    <img class="py-3" :src="selected_photo.src.large2x" style="width: 55vw; height: 50vh; object-fit: cover;" />

                    <div><strong>Photographer - {{selected_photo.photographer}}</strong></div>

                    <v-btn class="mt-2" target="_blank" outlined small :href="selected_photo.photographer_url">View Profile</v-btn>

                    
                </v-card-text>

                <v-divider></v-divider>

                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="success" class="btn btn-success mr-2" @click="download()" :disabled="downloading_image" :loading="downloading_image"> Download</v-btn>
                    <v-btn color="danger" @click="dialog = false">
                        Close
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    
  </div>
</template>

<style scoped>

.search_photos{
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

import axios from 'axios'


export default {
    name: "results",
    components: {

    },
    data() {
        return{
            photos: [],
            photo_page: 1,
            loading_photos: false,
            search_param: this.$route.params.search,
            dialog: false,
            check_if_search_is_empty: null,
            downloading_image: false,
            selected_photo: {
                photographer: '',
                photographer_url: '',
                src: {
                    large2x: '',
                }
            },
        }  
    },
    created() {
        this.load_search_parameter();
    },
    mounted() {
        
    },
    watch: {
        
    },
    computed: {
        
    },
    methods: {
        load_search_parameter(){
            this.loading_photos = true;
            axios
            .get(`https://api.pexels.com/v1/search?query=${this.$route.params.search}&per_page=20&page=${this.photo_page}`,{ 
                params: {},
                headers: {
                    "Authorization": "563492ad6f91700001000001daceeaf5b0f84a8299f1817f13f87e13"
                } 
            })
            .then(response => {
                this.photos = response.data.photos;
                this.loading_photos = false;

                if(this.photos.length == 0){
                    this.check_if_search_is_empty = true;
                }

                document.body.scrollTop = 0; // For Safari
                document.documentElement.scrollTop = 0; // For Chrome, Firefox, IE and Opera

                console.log("response", response);
            })
            .catch(()=>{
                this.loading_photos = false;
            });
        },

        load_photos(direction){
            if(direction == "previous"){
                this.photo_page = this.photo_page - 1;
            }
            if(direction == "next"){
                this.photo_page = this.photo_page + 1;
            }
            this.load_search_parameter();
        },

        search(){
            this.photo_page = 1;
            this.$route.params.search = this.search_param;

            this.$router.push({name: 'results', params:{search: this.search_param}});
            this.load_search_parameter();
        },

        select_photo(photo){
            this.selected_photo = photo;
            this.dialog = !this.dialog;

            console.log("photo", this.selected_photo);
        },

        download(){

            this.downloading_image = true;

            axios({
            url: this.selected_photo.src.original,
            method: 'GET',
            responseType: 'blob', // important
            }).then((response) => {
                const url = window.URL.createObjectURL(new Blob([response.data]));
                const link = document.createElement('a');
                link.href = url;
                link.setAttribute('download', this.selected_photo.photographer_id + '.jpeg'); //or any other extension
                document.body.appendChild(link);
                link.click();
                this.downloading_image = false;
            })
        },
    }
}

</script>