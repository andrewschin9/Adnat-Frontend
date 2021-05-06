<template>
  <div class="home">
    {{isLoggedIn()}}
    <p><a href="/signup">Sign up</a></p>
    <p><a href="/login">Login</a></p>
    <p><a href="/logout">Logout</a></p>
    <h1>ORGANIZATIONS</h1>
    <hr>
    <div v-for="organization in organizations">
      <h3>{{organization.name}}</h3>
      
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
      name: "",
      hourly_rate: "",
      currentOrganization: {},
      errors: [],
      search: "",
      search_hourly_rate: "",
    };
  },
  created: function () {
    console.log("in created");
    this.organizationsIndex();
  },
  methods: {
    organizationsIndex: function () {
      console.log("organizations index..");
      axios.get("/api/organizations").then((response) => {
        console.log(response.data);
        this.organizations = response.data;
      });
    },
    isLoggedIn: function () {
      if (localStorage.getItem("jwt")) {
        return "";
      } else {
        return "---> Must be logged in to view info <---";
      }
    },
  },
};
</script>
