<template>
  <div class="home">
    {{isLoggedIn()}}
    <p><a href="/signup">Sign up</a></p>
    <p><a href="/login">Login</a></p>
    <p><a href="/logout">Logout</a></p>
    <!-- List of all orgs -->
    <h1>ORGANIZATIONS</h1>
    <hr>
    <div v-for="organization in organizations">
      <!-- modal for making shifts -->
      <h3 v-on:click="showOrganization(organization)">Name: {{organization.name}}</h3>
      <h4>Hourly Rate: {{organization.hourly_rate}}</h4>
      <dialog id="organization-details">
        <form method = "dialog">
          <button class="btn-round">Close</button>
          <p>{{currentOrganization.name}}</p>
          <p>Shifts</p>
          <div v-for="shift in shifts">
            <h3>Employee name: </h3>
            <h3>Shift Date: {{shift.shift_date}}</h3>
            <h3>Shift start: {{shift.start_time}}</h3>  
            <h3>Shift finish: {{shift.finish_time}}</h3>  
            <h3>Break: {{shift.break_length}}</h3>  
            <hr>
          </div> 
        </form>
      </dialog>
      <hr>
    </div>
    <!-- Create new org -->
      <form v-on:submit.prevent="submit()">
      <h1>Make new organization</h1>
      <ul>
        <li class="text-danger" v-for="error in errors">{{ error }}</li>
      </ul>
      <div class="form-group">
        <label>Name:</label> 
        <input type="text" class="form-control" v-model="name">
      </div>
      <div class="form-group">
        <label>Hourly Rate:</label>
        <input type="text" class="form-control" v-model="hourly_rate">
      </div>
      <input type="submit" class="btn btn-primary" value="Submit">
    </form>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
import Vue2Filters from "vue2-filters";

export default {
  mixins: [Vue2Filters.mixin],
  data: function () {
    return {
      organizations: [],
      shifts: [],
      employee: "",
      shift_date: "",
      start_time: "",
      finish_time: "",
      break_length: "",
      shift_length: "",
      name: "",
      hourly_rate: "",
      currentOrganization: {},
      errors: [],
      search: "",
    };
  },
  created: function () {
    console.log("in created");
    this.organizationsIndex();
    this.shiftsIndex();
  },
  methods: {
    shiftsIndex: function () {
      axios.get("/api/shifts").then((response) => {
        console.log(response.data);
        this.shifts = response.data;
      });
    },

    organizationsIndex: function () {
      console.log("organizations index..");
      axios.get("/api/organizations").then((response) => {
        console.log(response.data);
        this.organizations = response.data;
      });
    },
    showOrganization: function (organization) {
      this.currentOrganization = organization;
      document.querySelector("#organization-details").showModal();
    },
    isLoggedIn: function () {
      if (localStorage.getItem("jwt")) {
        return "";
      } else {
        return "---> Must be logged in to view/create organizations <---";
      }
    },
    // shiftLength: function () {
    //   this.shift_length =
    //     this.shift.finish_time.to_i - this.shift.start_time.to_i;
    // },
    submit: function () {
      var params = {
        name: this.name,
        hourly_rate: this.hourly_rate,
      };
      axios
        .post("/api/organizations", params)
        .then((response) => {})
        .catch((error) => {
          this.errors = error.response.data.errors;
          console.log(error.response.status);
          this.status = error.response.status;
        });
      this.organizationsIndex();
    },
  },
};
</script>
