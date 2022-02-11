<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Oh the places Ro will go!",
      places: [],
      viewPlace: {},
      editPlace: {},
      newPlaceParams: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      console.log("sanity check");
      axios.get("/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function (object) {
      axios
        .post("/places", object)
        .then((response) => {
          console.log("Success!", response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (object) {
      axios.get(`/places/${object.id}`, object).then((response) => {
        console.log(response.data);
        this.viewPlace = response.data;
        this.editPlace = response.data;
        document.querySelector("#more-info").showModal();
      });
    },
    updatePlace: function (object) {
      axios
        .patch(`places/${object.id}`, object)
        .then((response) => {
          console.log("Success!", response.data);
        })
        .catch((error) => console.log(error.response.data.errors));
    },
    destroyPlace: function (object) {
      axios.delete(`/places/${object.id}`, object).then((response) => {
        console.log(response.data.message);
        var index = this.places.indexOf(object);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div>
      <p>
        Name:
        <input type="text" v-model="newPlaceParams.name" />
      </p>
      <p>
        Address:
        <input type="text" v-model="newPlaceParams.address" />
      </p>
      <button v-on:click="createPlace(newPlaceParams)">Add New Place</button>
      <div v-for="error in errors" v-bind:key="error">{{ error }}</div>
    </div>
    <br />
    <div v-for="place in places" v-bind:key="place.id">
      <p>{{ place.name }}</p>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
      <br />
    </div>
    <dialog id="more-info">
      <form method="dialog">
        <h2>{{ viewPlace.id }}</h2>
        <p>
          Name:
          <input type="text" v-model="editPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="editPlace.address" />
        </p>
        <br />
        <button v-on:click="updatePlace(editPlace)">Update</button>
        <button v-on:click="destroyPlace(editPlace)">Delete</button>
        <button>Exit</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
