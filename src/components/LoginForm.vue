<template>
  <div>
    <empty-navbar></empty-navbar>
    <div class="login-container flex-center">
      <form class="login-form bg-dark-tr" @submit.prevent="sigin()" @click="toggleSiginError">
        <div class="group">
          <input type="email" class="bg-none" :class="{error:  inactiveUser || siginError}" required v-model="email" />
          <span class="highlight"></span>
          <span class="bar"></span>
          <label class="transform-label" :class="{error:  inactiveUser || siginError}">Email</label>
        </div>
        <div class="group mb-25">
          <input type="password" class="bg-none" :class="{error:  inactiveUser || siginError}" required v-model="pass" />
          <span class="highlight"></span>
          <span class="bar"></span>
          <label class="transform-label">Password</label>
        </div>
        <div :class="{'space-between': siginError}">
          <label v-show="siginError" class="text-danger">Wrong password or email. Please try again.</label>
          <input type="submit" value="Sign in" class="btn btn-danger btn-sigin" />
        </div>
        <div :class="{'space-between': inactiveUser && siginError}">
          <label v-show="inactiveUser" class="text-danger">User inactive. Contact to administrator.</label>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import EmptyNavbar from "./EmptyNavbar";
import Vue from "vue";
import VueSession from 'vue-session';

Vue.use(VueSession)
const api = "http://localhost:3000/api";

export default {
  data() {
    return {
      email: "",
      pass: "",
      siginError: false,
      isAdmin: false,
      inactiveUser: false
    };
  },
  components: {
    "empty-navbar": EmptyNavbar
  },
  methods: {
    sigin() {
      const loggedUser = {
        email: this.email,
        pass: this.pass
      };
      axios
        .post(api + "/sigin", loggedUser)
        .then((response) => {
          const user = response.data.foundUser;
          const accessToken = response.data.token.access_token;
          
          // inactive user
          if (user.statusId != 0) {
            this.$session.set('user', user);
            this.$session.set('token', accessToken);
            if (user.roleId == 1) {
              this.navigate("/pending-queries");
            } else {
              this.navigate("/my-queries");
            }
          } else {
            this.inactiveUser = true;
          }
        })
        .catch((error) => {
          this.siginError = true;
          console.log("error", error);
        });
    },
    toggleSiginError() {
      this.siginError = false;
      this.inactiveUser = false;
    }
  }
};
</script>

<style>
.space-between {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.login-container {
  background-image: url("./../assets/banner.jpg");
  background-repeat: no-repeat;
  background-size: cover;
  height: 90vh;
  padding-bottom: 5%;
}

.login-form {
  padding: 40px;
  padding-top: 48px;
  border-radius: 12px;
  box-shadow: 7px 5px 15px -4px rgba(0, 0, 0, 0.75);
  width: 35%;
}

.bg-dark-tr {
  background: rgb(52, 58, 64, 0.6);
}

.btn-sigin {
  width: 30%;
  float: right;
  min-width: 100px;
}

@media (max-width: 320px) {
  .login-form {
    width: 80%;
  }
}

@media (max-width: 576px) {
  .login-form {
    width: 80%;
  }
}

@media (max-width: 768px) {
  .login-form {
    width: 70%;
  }
}
</style>