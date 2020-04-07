<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark vh-10">
    <router-link :to="navigateHome" class="navbar-brand">
      <img src="http://www.go-sharp.net/img/Logo-horizontal.png" alt="go-sharp logo" class="w-10" />
    </router-link>
    <button
      class="navbar-toggler"
      type="button"
      data-toggle="collapse"
      data-target="#navbarSupportedContent"
      aria-controls="navbarSupportedContent"
      aria-expanded="false"
      aria-label="Toggle navigation"
    >
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav mr-auto text-center ">
        <router-link tag="li" active-class="active" to="query-form" class="nav-link cursor" v-if="isLogged && !isAdmin">Permissions</router-link>
        <router-link tag="li" active-class="active" to="my-queries" class="nav-link cursor" v-if="isLogged && !isAdmin">My Queries</router-link>
        <router-link tag="li" active-class="active" to="pending-queries" class="nav-link cursor" v-if="isLogged && isAdmin">Pending Queries</router-link>
        <router-link tag="li" active-class="active" to="user-list" class="nav-link cursor" v-if="isLogged && isAdmin">User list</router-link>
      </ul>
      <div class="form-inline my-2 my-lg-0 navbar-nav tex-left">
        <a class="nav-link" href="#" v-if="isLogged" @click="signOut">
          Sign out
        </a>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  computed: {
    isAdmin() {
      return this.$session.get('user').roleId == 1;
    },
    isLogged() {
      return this.$session.get('user');
    },
    navigateHome() {
      if (this.$session.get('user').roleId == 1) {
          return '/pending-queries';
        } else {
          return '/my-queries';
      }
    }
  },
  methods: {
    signOut() {
      this.$session.destroy();
      this.$router.push("/");
    }
  }
};
</script>

<style>
.w-10 {
  width: 10em;
}

.vh-10 {
  min-height: 10vh;
}

li.nav-item.active {
  box-sizing: initial;
}

a.nav-item {
  text-align: right;
}
</style>