<template>
  <div class="app">
    <br />

    <b-container fluid class="container mt-4">
      <b-row class="d-flex inline-flex justify-content-start">
        <b-col class="mr-4 ml-3">
          <img :src="store.userPicture"  alt="profile pic" class="avatar">
          <h3 class="mt-3">{{ store.displayName }}</h3>
          <router-link to="/editprofile">
            <button type="button" class="btn btn-secondary">
              Edit Profile
            </button>
          </router-link>
        </b-col>

        <b-col cols="8">
          <b-row no-gutters>
            <b-col>
              <router-link to="/dashboard/overview" class="nav-link active text-light active"><img src="@/assets/overview.png" class="mr-1" height="20">Overview</router-link>
            </b-col>
            
            <b-col>
              <router-link
                to="/dashboard/workouts"
                class="nav-link text-light active"
                ><img
                  src="@/assets/dumbbell.png"
                  class="mr-1"
                  height="20"
                />Workouts</router-link
              >
            </b-col>

            <b-col>
              <router-link to="/dashboard/myplan" class="nav-link text-light"
                ><img src="@/assets/pencil.png" class="mr-1" height="20" />My
                Plan</router-link
              >
            </b-col>

            <b-col>
              <router-link to="/dashboard/pics" class="nav-link text-light"
                ><img
                  src="@/assets/image.png"
                  class="mr-1"
                  height="20"
                />Pics</router-link
              >
            </b-col>

            <b-col>
              <router-link to="/dashboard/BMI" class="nav-link text-light"
                ><img
                  src="@/assets/bmi1.png"
                  class="mr-1"
                  height="20"
                />BMI</router-link
              >
            </b-col>
          </b-row>
          <b-row class="my-1 mt-4">
            <b-col sm="3" class="mt-2">
              <label :for="`type-Height`"> Height </label>
            </b-col>
            <b-col sm="5">
              <b-form-input :id="`type-Height`" v-model="Height" placeholder="Enter your height in cm"></b-form-input>
            </b-col>
          </b-row>
          <b-row class="my-1 mt-3">
            <b-col sm="3" class="mt-2">
              <label :for="`type-Weight`"> Weight </label>
            </b-col>
            <b-col sm="5">
              <b-form-input :id="`type-Weight`" v-model="Weight" placeholder="Enter your weight in kg"></b-form-input>
            </b-col>
          </b-row>

          <b-row>
            <b-col sm="5" style="left: 25%">
              <b-button class="mb-2 mt-2" variant="primary" @click="bmiToast()">
                Calculate
              </b-button>
              <b-toast
                id="example-toast"
                title="Results say that.."
                static
                no-auto-hide
              >
                Your BMI index is {{ bmi }}
              </b-toast>
               <b-row class="my-1 mt-3">
                  <img class="presenting-bmi" src=@/assets/bmi-chart.png style="right:-40%"/>
               </b-row>
            </b-col>
          </b-row>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
  import { firebase } from "@/firebase";
  import store from "@/store";

  export default {
    data() {
      return {
        store,
        bmi: null,
        Height: null,
        Weight: null,
        types: ["Height", "Weight"],
      };
    },
    methods: {
      bmiToast(append = false) {
        this.bmi = (
          Number(this.Weight) /
          Number((this.Height / 100) * (this.Height / 100))
        ).toFixed(2);
        this.$bvToast.toast(`${this.bmi}`, {
          title: "Your BMI",
          autoHideDelay: 5000,
          toaster: "b-toaster-top-center",
          appendToast: append,
        });
      },
    },
  };
</script>

<style scoped>
  .nav-link::after{
    content: '';
    display: block;
    width: 0;
    height: 2px;
    background: #fff;
    transition: width .3s;
    margin-top: 10px;
  }
  .nav-link:hover::after{
    width: 100%;
    transition: width .7s;
  }
  .image {
    margin-left: -120px;
  }
  .container {
    margin-left: -150px;
  }
  a.router-link-active {
    background: gray;
  }
  .avatar {
    width: 300px;
    height: 300px;
    border-radius: 50%;
  }
  .presenting-bmi{
    position: absolute;
    height: 200px;
  }
</style>
