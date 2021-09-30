<template>
  <div class="search">
   <h1>{{msg}}</h1>
   <form v-on:submit.prevent="getResult(rover,date)">
     <select v-model="rover" class="input">
       <option disabled value="">Select a Rover</option>
       <option value="curiosity">Curiosity</option>
       <option value="opportunity">Opportunity</option>
       <option value="spirit">Spirit</option>
       </select>
    <input type="date" placeholder="Type your search" v-model="date" class="input"/>
    <input type="submit" value="Search" class="input"/>
    </form>
    <div v-if="results">
      <div v-model="results">
      There are {{results.total_photos}} photos
      </div>
      <div v-for="camera in results.cameras">
        <button v-on:click="getPhotos(camera,rover,photos)">{{cameraNames[camera]}}</button>
        </div>
      </div>
      <div v-else>
        <h1>No Photos</h1>
        </div>
        <div v-if="photos">
          <div v-for="photo in photos" class="image-holder">
            <img v-bind:src="photo.img_src" class="image"/>
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
          this.photos = response.data.photos;
        })
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
