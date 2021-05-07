<template>
  <div class="home">
    {{isLoggedIn()}}
    <p><a href="/signup">Sign up</a></p>
    <p><a href="/login">Login</a></p>
    <p><a href="/logout">Logout</a></p>
    <p><a href="/myshifts">My Shifts</a></p>
    <!-- List of all orgs -->
    <h1>ORGANIZATIONS</h1>
    <hr>
    <div v-for="organization in organizations">
      <!-- modal for making shifts -->
      <h3 v-on:click="showOrganization(organization); filterShifts(organization)">Name: {{organization.name}}</h3>
      <h4>Hourly Rate: {{organization.hourly_rate}}</h4>
      <dialog id="organization-details">
        <form method = "dialog">
          <button class="btn-round">Close</button>
          <p>{{currentOrganization.name}}</p>
          <p>Shifts</p>
          <div v-for="filteredShift in filteredShifts">
            <h3>Employee name: {{filteredShift.employee_name}}</h3>
            <h3>Shift Date: {{filteredShift.shift_date}}</h3>
            <h3>Shift start: {{filteredShift.start_time}}</h3>  
            <h3>Shift finish: {{filteredShift.finish_time}}</h3>  
            <h3>Break (in min): {{filteredShift.break_length}}</h3>  
            <h3>Length (in hr): {{filteredShift.length}}</h3>  
            <h3>Wage (in $): {{filteredShift.wage}}</h3>  
            <hr>
          </div> 
          <!-- Make new shifts -->
          <form v-on:submit.prevent="createShift()">
            <h3>New Shift</h3>
            <ul>
              <li class="text-danger" v-for="error in errors">{{ error }}</li>
            </ul>
            <div class="form-group">
              <label>Shift Date (2XXX-12-31):</label> 
              <input type="text" class="form-control" v-model="shift_date">
            </div>
            <div class="form-group">
              <label>Shift Start (X:XXam or pm):</label>
              <input type="text" class="form-control" v-model="start_time">
            </div>
            <div class="form-group">
              <label>Shift finish (X:XXam or pm):</label>
              <input type="text" class="form-control" v-model="finish_time">
            </div>
            <div class="form-group">
              <label>Break (in min):</label>
              <input type="text" class="form-control" v-model="break_length">
            </div>
            <input type="submit" class="btn btn-primary" value="Submit">
          </form>
        </form>
      </dialog>
      <hr>
    </div>
    <!-- Make new org -->
      <form v-on:submit.prevent="submitOrg()">
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
      length: "",
      name: "",
      hourly_rate: "",
      currentOrganization: {},
      currentShift: {},
      errors: [],
      search: "",
      filteredShifts: [],
    };
  },
  computed: {},

  created: function () {
    console.log("in created");
    this.organizationsIndex();
    this.shiftsIndex();
  },
  methods: {
    shiftLength(filteredShift) {
      return typeof filteredShift.start_time;
    },
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
    submitOrg: function () {
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
    filterShifts: function (organization) {
      var fshift = this.shifts.filter(
        (item) => item.organization_id === organization.id
      );
      this.filteredShifts = fshift;
    },
    createShift: function () {
      var params = {
        organization_id: this.currentOrganization.id,
        shift_date: this.shift_date,
        start_time: this.start_time,
        finish_time: this.finish_time,
        break_length: this.break_length,
      };
      axios.post("http://localhost:3000/api/shifts", params);
    },
  },
};
</script>
