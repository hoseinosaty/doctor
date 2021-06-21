<template>
  <div id="app">
    <div class="toolbar">
      <span class="toolbar-logo">Doctors</span>
      <div class="spacer"></div>
      <div class="navigation">
        <a @click="showDialog = true" href="javascript:void(0)" class="nav-link"
          >Add profile
        </a>
        <input
          placeholder="Search doctor"
          v-model="searchString"
          @keydown="SearchByName"
          type="search"
        />
      </div>
    </div>
    <div class="content">
      <div class="page-header">
        <div class="page-header-title">Profiles</div>
        <div class="page-header-operations">
          <button @click="sortAsc()">
            <i class="fa fa-sort-alpha-up-alt"></i>
          </button>
          <button @click="sortDesc()">
            <i class="fa fa-sort-alpha-down"></i>
          </button>
        </div>
      </div>
      <div class="profile-card-container">
        <profile-card
          v-for="(i, index) in profiles"
          :key="index"
          :profile="i"
        ></profile-card>
      </div>
    </div>
    <div
      class="add-profile-dialog"
      :style="'display:' + (showDialog ? 'flex' : 'none')"
    >
      <div class="add-profile-form-holder">
        <div class="add-profile-form-toolbar">
          <div class="add-profile-form-title">Add profile</div>
          <div><button @click="showDialog = false">Close</button></div>
        </div>
        <div class="form-row">
          <multiselect
            v-model="form.specialisation"
            :options="options"
            :multiple="true"
            :close-on-select="false"
            :clear-on-select="false"
            :preserve-search="true"
            placeholder="Specialisation"
            label="name"
            track-by="name"
            :preselect-first="true"
          >
            <template slot="selection" slot-scope="{ values, isOpen }"
              ><span
                class="multiselect__single"
                v-if="values.length &amp;&amp; !isOpen"
                >{{ values.length }} options selected</span
              ></template
            >
          </multiselect>
        </div>
        <div class="form-row">
          <input
            type="text"
            v-on:keypress="isLetter($event)"
            class="input"
            v-model="form.name"
            placeholder="Name"
          />
        </div>
        <div class="form-row">
          <input
            type="email"
            class="input"
            v-model="form.email"
            placeholder="Email"
          />
        </div>
        <div class="form-row">
          <textarea
            class="input"
            style="height: 50px"
            v-model="form.description"
            placeholder="Description"
          ></textarea>
        </div>
        <div class="form-row">
          <button @click="SaveProfile">Save</button>
        </div>
      </div>
    </div>
  </div>
</template>
<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
<script>
import "../src/assets/css/fonts.css";
import "@fortawesome/fontawesome-free/css/all.css";
import "@fortawesome/fontawesome-free/js/all.js";
import "./assets/css/style.css";
import Multiselect from "vue-multiselect";
import ProfileCard from "./components/ProfileCard.vue";
var defaultForm = Object.freeze({
  name: "",
  specialisation: [],
  email: "",
  description: "",
  likeCount: 0,
  disLikeCount: 0,
  commentCount: 0,
  id: new Date().getTime(),
});
export default {
  name: "app",
  components: {
    ProfileCard,
    Multiselect,
  },
  data: () => ({
    showDialog: false,
    form: Object.assign({}, defaultForm),
    value: [],
    options: [
      { name: "Surgeon" },
      { name: "Radiologist" },
      { name: "Cardiologist" },
      { name: "Psychiatrist" },
      { name: "Dermatologist" },
    ],
    profiles: [],
    searchString: "",
    repository: [],
  }),
  created() {
    this.loadProfiles();
  },
  methods: {
    sortAsc() {
      let vm = this;
      vm.profiles = vm.profiles.sort(function (a, b) {
        return parseInt(a.likeCount) - parseInt(b.likeCount);
      });
    },
    sortDesc() {
      let vm = this;
      vm.profiles = vm.profiles.sort(function (a, b) {
        return parseInt(b.likeCount) - parseInt(a.likeCount);
      });
    },
    SearchByName() {
      var vm = this;
      vm.profiles = vm.repository.filter((x) =>
        x.name.includes(vm.searchString.toLowerCase())
      );
    },
    isLetter(e) {
      let char = String.fromCharCode(e.keyCode); // Get the character
      if (/^[A-Za-z]+$/.test(char)) {
        return true;
      } // Match with regex
      else {
        alert("Only english char allowed");
        e.preventDefault();
      } // If not match, don't add to input text
    },
    validateEmail(email) {
      const re =
        /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(String(email).toLowerCase());
    },
    SaveProfile() {
      let vm = this;
      if (vm.form.name == "" || vm.form.name == null) {
        alert("Name is required!");
        return;
      }
      if (!vm.validateEmail(vm.form.email)) {
        alert("Email is incorrect");
        return false;
      }
      vm.form.name = vm.form.name.toLowerCase();
      vm.form.id = new Date().getTime();
      var desrilise = [];
      var getSavedItems = window.localStorage.getItem("profiles");
      if (getSavedItems != null) {
        desrilise = JSON.parse(getSavedItems);
        desrilise.push(vm.form);
      } else {
        desrilise.push(vm.form);
      }
      window.localStorage.setItem("profiles", JSON.stringify(desrilise));
      vm.form = Object.assign({}, defaultForm);
      vm.showDialog = false;
      vm.loadProfiles();
    },
    reload() {
      alert("Called");
    },
    loadProfiles() {
      let vm = this;
      var getSavedItems = window.localStorage.getItem("profiles");
      if (getSavedItems != null) {
        vm.profiles = JSON.parse(getSavedItems);
        vm.repository = vm.profiles;
        console.warn("PROFILES", vm.profiles);
      }
    },
  },
};
</script>
