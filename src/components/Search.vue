<template>
  <div class="container-flex p-5 wrapper bg-dark">
    <img src="https://www.nasa.gov/sites/all/themes/custom/nasatwo/images/nasa-logo.svg" class="header-image"/>
<h1 class="text-white mb-3 header">Mars Rover Gallery</h1>
   <div class="card p-5 m-5">
   <p>Welcome to the Mars Rover Gallery, brought to you by <a href="https://www.nasa.gov/" target="_blank" class="text-decoration-none text-primary">NASA Open API's</a>.</p>
   <p>To get started, please select from one of the three available rovers.</p>
   <form v-on:submit.prevent="getResult(rover,date)">
     <div class="d-flex justify-content-center align-items-end">
       <div class="col-2 m-3">
         <label class="form-label">Rover</label>
     <select v-model="rover" class="form-select">
       <option disabled value="">Select a Rover</option>
       <option value="curiosity">Curiosity</option>
       <option value="opportunity">Opportunity</option>
       <option value="spirit">Spirit</option>
       </select>
       </div>
       <div class="col-2 m-3">
         <label class="form-label">Date of Images</label>
    <input type="date" placeholder="Type your search" v-model="date" class="form-control"/>
       </div>
       <div class="col-2 m-3">
    <input type="submit" value="Search" class="btn btn-primary form-control"/>
       </div>
       </div>
    </form>
    <div v-if="results">
      <div v-model="results">
      {{results>0 && `There are ${results.total_photos} photos` || "" }}
      </div>
      <div class="row mb-3 mt-3">
        <div v-if="results.cameras">
          <label class="form-label">Please select a camera to view</label>
          </div>
          <div class="justify-content-center row">
      <div v-for="camera in results.cameras" class="col-3 mb-3">
        <button v-on:click="getPhotos(camera,rover,photos)" class="btn btn-primary w-100">{{cameraNames[camera]}}</button>
        </div>
        </div>
        </div>
      </div>
      <div v-else>
        <h1 class="">No Photos</h1>
        </div>
        <div v-if="photos" class="card bg-light p-3">
          <div class="row">
          <div v-for="photo in photos" class="image-holder col-4 mb-3">
            <img v-bind:src="photo.img_src" class="image"/>
            </div>
          </div>
          </div>
  </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Search',
  data () {
    return {
      msg: 'Search',
      date: '',
      rover: '',
      camera: '',
      results: {},
      photos: '',
      cameraNames: {
        FHAZ: 'Front Hazard Avoidance Camera',
        RHAZ: 'Rear Hazard Avoidance Camera',
        MAST: 'Mast Camera',
        CHEMCAM: 'Chemistry and Camera Complex',
        MAHLI: 'Mars Hand Lens Imager',
        MARDI: 'Mars Decent Imager',
        NAVCAM: 'Navigation Camera',
        PANCAM: 'Panoramic Camera',
        MINITES: 'Minitature Thermal Emission Spectrometer (Mini-TES)',
      }
    }
  },
  methods: {
    getResult(rover,date){
      axios.get('https://api.nasa.gov/mars-photos/api/v1/manifests/'+rover+'?api_key=qYavjcnrZOaoLo8ayJofWJx3duMuedGFLgffRNif').then(
        response => {
          console.log( response.data.photo_manifest.photos.filter(photo => photo.earth_date == date)[0]);
          this.results = response.data.photo_manifest.photos.filter(photo => photo.earth_date == date)[0];
        })
    },
    getPhotos(camera){
            axios.get('https://api.nasa.gov/mars-photos/api/v1/rovers/'+this.rover+'/photos?earth_date='+this.date+'&camera='+camera+'&api_key=qYavjcnrZOaoLo8ayJofWJx3duMuedGFLgffRNif').then(
        response => {
          console.log( response.data.photos);
          this.camera = camera
          this.photos = response.data.photos.slice(0,25);
        })
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
