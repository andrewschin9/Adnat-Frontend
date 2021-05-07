<template>
  <div class="home">
    {{isLoggedIn()}}
    <p><a href="/signup">Sign up</a></p>
    <p><a href="/login">Login</a></p>
    <p><a href="/logout">Logout</a></p>
    <!-- List of all your shifts -->
    <h1>MY SHIFTS</h1>
    <hr>
    <div v-for="filteredShift in filteredShifts">
      <h3>Employee name: {{filteredShift.employee_name}}</h3>
      <h3>Shift Date: {{filteredShift.shift_date}}</h3>
      <h3>Shift start: {{filteredShift.start_time}}</h3>  
      <h3>Shift finish: {{filteredShift.finish_time}}</h3>  
      <h3>Break (in min): {{filteredShift.break_length}}</h3>  
      <h3>Length (in hr): {{filteredShift.length}}</h3>  
      <h3>Wage (in $): {{filteredShift.wage}}</h3>  
      <button v-on:click="deleteShift(filteredShift)">Delete</button>
      <hr>

    </div> 
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
      currentShift: {},
      errors: [],
      search: "",
      filteredShifts: [],
    };
  },
  created: function () {
    console.log("in created");
    this.shiftsIndex();
  },
  methods: {
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
    shiftsIndex: function () {
      axios.get("/api/shifts").then((response) => {
        console.log(response.data);
        this.shifts = response.data;
        this.filterShifts();
      });
    },
    filterShifts: function () {
      console.log(this.shifts);
      var fshift = this.shifts.filter(
        (item) => item.employee_id === parseInt(localStorage.getItem("userId"))
      );
      this.filteredShifts = fshift;
      console.log(fshift);
      console.log(localStorage.getItem("userId"));
    },
    deleteShift: function (filteredShift) {
      axios.delete("/api/shifts/" + filteredShift.id).then((response) => {
        console.log(response.data);
        this.shiftsIndex();
      });
    },
  },
};
</script>
