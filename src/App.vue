<template>
<div id="app">
    <nav class="navbar navbar-expand-lg navbar-dark">
      <router-link to="/" class="navbar-brand">
        <img src="@/assets/gymbo_logo.png" height="50" class="d-inline-block align-top" alt="" loading="lazy" />
      </router-link>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
    <div class="collapse navbar-collapse" id="navbarNavDropdown">
      <ul class="navbar-nav ml-auto">
        <li v-if="store.currentUser" class="nav-item">
          <router-link to="/dashboard/overview" class="nav-link text-light mr-2" style="text-transform: uppercase">Dashboard</router-link>
        </li>
        <li v-if="!store.currentUser" class="nav-item">
            <router-link to="/login" class="nav-link text-light">Login</router-link>
          </li>
        <li v-if="!store.currentUser" class="nav-item">
          <router-link to="/register" class="nav-link text-light">Register</router-link>
        </li>
        <li v-if="store.currentUser" class="nav-item mr-3">
          <img src="@/assets/notification.png" class="notification" @click="showToast" width="34" height="34"/>
        </li>
        <li v-if="store.currentUser" class="nav-item mt-1 mr-3">
          <a href="https://gymbofipu.wordpress.com/" target="_blank"><img src="@/assets/shopping-cart.png" class="shopping-cart" width="34" height="34"/></a>
        </li>
         <li v-if="store.currentUser" class="nav-item">
          <a href="#" @click.prevent="logout()" class="nav-link text-light" style="text-transform: uppercase">Logout</a>
        </li>
        <li v-if="store.currentUser" class="nav-item">
          <img :src="store.userPicture" alt="profile pic" variant="primary" @click="makeToast('primary')" style="border-radius:50%" width="35" height="35" />
        </li>
      </ul>
    </div>
    
  </nav>
    <div class="container">
      <router-view/>
    </div>
  </div>
</template>

<script>
import store from "@/store";
import { firebase } from "@/firebase";
import { db } from "@/firebase";
import router from "@/router";

/* firebase.auth().onAuthStateChanged((user) => {
  const currentRoute = router.currentRoute;

  console.log('Provjera stanja logina!');
  if (user) {
    // User is signed in.
    console.log(user.email);
    store.currentUser = user.email;

    if(!currentRoute.meta.needsUser) {
      router.push({ name: "Home" })
    }
  } else {
    // User is not signed in.
    console.log('No user');
    store.currentUser = null;


    if(currentRoute.meta.needsUser) {
      router.push({ name: "Login" })
    }
   if(router.name !== 'login'){
    router.push({ name: "Login" })
    } 
    console.log("***", "no user");
    store.currentUser = null;    
  }
});  */

export default {
  name: "app",
  data() {
    return {
      store, 
      workoutHours: 18,
    };
  },

  mounted(){
    this.created();
  },
  
  methods: {
    makeToast() {
        this.$bvToast.toast(store.currentUser, {
          title: 'User:',
          size: 'sm',
          buttonSize: 'sm',
          okVariant: 'success',
        })
      },
    logout() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          this.$router.push({ name: "Login" });
        });
    },

      howLongUntil() {
        let date = new Date()
        const hours = date.getHours()
        const minutes = date.getMinutes()
        let hoursLeft = 0
        let minutesLeft = 0
        const targetHour = 18
        let str = ''
        //22:55
        //is it before or after 18:00 right now?
        //it is 18:00 or later
        if(hours >= targetHour){
          //it's after 18:00, how many hours in the rest of the day
          hoursLeft = 24 - hours //24-22= 2
          //if we are over 0 minutes, add 1 hour from remaining day hours
          //if over 0 minutes, find the minutes difference
          if(minutes > 0){
            hoursLeft--
            //2-1= 1 hour
            minutesLeft =  60 - minutes
            //60-55=5 minutes
            //1 hour & 5 minutes till midnight
          }
          //add 18 hours to get tomorrows hours + todays remaining hours
          hoursLeft = hoursLeft + 18
          //1+18=19 hours
          //19 hours and 5 minutes until 18:00 tomorrow
          str = hoursLeft + " hours & " + minutesLeft + " minutes"
        } else {
          //it's before 6pm, so this will be more normal
          //how many hours before 18 is it right now?
          hoursLeft = targetHour - hours
          //if you are any minutes into the hour, deduct 1 hour
          //also find number of minutes left
          if(minutes > 0){
            hoursLeft--
            minutesLeft =  60 - minutes
          }
          str = hoursLeft + " hours &" + minutesLeft + " minutes"
        }
        return str;
      },

      showToast() {
          const timeMessage = this.howLongUntil()
          console.log(timeMessage)
          // Use a shorter name for this.$createElement
          const w = this.$createElement
          // Create the message
          const vNodesMsg = w(
            'p',
            { class: ['text-center', 'mb-0'] },
            [w('b-spinner', { props: { type: 'grow', small: true } }),
               ' Your workout begins in ' + timeMessage,
              w('b-spinner', { props: { type: 'grow', small: true } })
            ]
          )
          // Create the title
          const vNodesTitle = w(
            'div',
            { class: ['d-flex', 'flex-grow-1', 'align-items-baseline', 'mr-2'] },
            [ w('strong', { class: 'mr-2' }, 'Workout Reminder'),
              w('small', { class: 'ml-auto text-italics' }, 'Just now')
            ]
          )
          // Pass the VNodes as an array for message and title
          this.$bvToast.toast([vNodesMsg], {
            title: [vNodesTitle],
            solid: true,
            variant: 'info'
          })
        },

    created(){
      const self = this;
      firebase.auth().onAuthStateChanged((user) => {
        const currentRoute = router.currentRoute;
        
        console.log("Login checking");

        if (user){
          self.authenticated = true
          console.log("Current user - ", user.email)
          store.currentUser = user.email;
          db.collection("Users")
            .doc(self.store.currentUser)
            .get()
            .then(doc => {
              if (doc.exists){
                console.log("Document data: ", doc.data());
                store.displayName = doc.data().name;
                store.currentUser = doc.data().email;
                store.password = doc.data().password;
                store.userPicture = doc.data().userPicture;
              }
              else{
                console.log("Document undefined");
              }
            });
        }else{
          console.log("User unknown");
          store.currentUser = null;
        }
      });
    },
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  background-color: black;
  height: 140vh;
  background-image: linear-gradient(rgba(0, 0, 0, 0.85), rgba(0, 0, 0, 0.85)),url('./assets/background.jpg');
  background-size: cover;
}

.navbar-nav{
  justify-content: end;
}
#nav {
  padding: 30px;
}

.notification, .shopping{
  cursor: pointer;
  cursor: hand;
}
</style>