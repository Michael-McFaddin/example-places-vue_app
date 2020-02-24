<template>

  <div class="home">
    <h1>{{ message }}</h1>
    <!-- index action -->
    <div v-for="place in places">
      <h2>{{ place.name }}</h2>
       <img src="https://cdn.pixabay.com/photo/2016/08/08/09/17/avatar-1577909_960_720.png" alt=""><br>
       <!-- this shows how the toggle works, shows current state of showInfo -->
       place.showInfo: {{ place.showInfo }}
      
      <!-- show action -->
      <div v-if="place.showInfo">
         <h3>{{ place.address }}</h3>  
         <div>
          <!-- update action -->
          <h4>Edit Place</h4>

          <ul>
            <li v-for="error in updateErrors">{{ error }}</li>
          </ul>

          Name: <input type="text" v-model="place.name"><br>
          Address: <input type="text" v-model="place.address"><br>
          <button v-on:click="updatePlace(place)">Update</button>
          <button v-on:click="destroyPlace(place)">Delete</button>
        </div>
      </div><br>
      
      <button v-on:click="place.showInfo = !place.showInfo">More Info</button><br>
  
    </div>

    <!-- create action -->
    <div>
      <h3>Add Place</h3>
      <ul>
        <li v-for="error in createErrors">{{ error }}</li>
      </ul>
      Name: <input type="text" v-model="newPlaceName"><br>
      Address: <input type="text" v-model="newPlaceAddress"><br>
      <button v-on:click="createPlace()">Create</button>
    </div>
  </div>

</template>

<style>
img {
  width: 150px;
}
</style>

<script>
import axios from 'axios';
export default {
  data: function() {
    return {
      message: "Mike's Places App",
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      createErrors: [],
      updateErrors: []
    };
  },
  // created section happens as soon as page gets loaded
  //index and show actions
  created: function() {
    axios.get("/api/places").then(response => {
      response.data.forEach(place => {
        place.showInfo = false;
      });
      console.log(response.data);
      this.places = response.data;
    });
  },
  // create, update and destroy actions
  methods: {
    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };
      // console.log("Create place here.");
      axios.post("/api/places", params).then(response => {
        console.log("Success!", response.data);
        this.places.push(response.data);
        // this is pushing the new place info into the db
      })
        .catch(error => {
          console.log(error.response.data.errors);
          // this.errors.push(error.response.data.errors);
          this.createErrors = error.response.data.errors;
        });
    },
    updatePlace: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios.patch(`/api/places/${place.id}`, params)
        .then(response => {
          console.log("Success!", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          // this.errors.push(error.response.data.errors);
          this.updateErrors = error.response.data.errors;
        });
    },
    destroyPlace: function(place) {
      axios.delete(`/api/places/${place.id}`)
        .then(response => {
          console.log("Successfully deleted", response.data);
          // finds index of place object in places array. id and index don't match necessarily
          var index = this.places.indexOf(place);
          console.log(index);
          // remove that index from the places array
          this.places.splice(index, 1);
        });
    }
  }
};
</script>
