<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>
import QueryForm from "./components/QueryForm.vue";
import LoginForm from "./components/LoginForm.vue";
import MyQueries from "./components/MyQueries.vue";
import PendingQueries from "./components/PendingQueries.vue";
import Vue from "vue";
import VueRouter from "vue-router";
import UserList from "./components/UserList.vue";

Vue.use(VueRouter);

const router = new VueRouter({
  routes: [
    {
      name: 'Login',
      path: "/",
      component: LoginForm
    },
    {
      path: "/query-form",
      component: QueryForm
    },
    {
      path: "/my-queries",
      component: MyQueries
    },
    {
      name: "PendingQueries",
      path: "/pending-queries",
      component: PendingQueries
    },
    {
      name: 'UserList',
      path: "/user-list",
      component: UserList
    }
  ]
});

Vue.mixin({
  methods: {
    navigate(route) {
      router.push(route).catch(err => { 
        throw new Error(`Problem handling something: ${err}.`);
      });
    }
  }
});

router.beforeEach((to, from, next) => {
  try {
  const token = sessionStorage.getItem('vue-session-key') ? JSON.parse(sessionStorage.getItem('vue-session-key')).token : undefined;  
    if (to.name !== 'Login' && !token) {
      next({ name: 'Login' })
    } else {
      next();
    }

    if (to.name === 'UserList' || to.name === 'PendingQueries') {
      const isAdmin = sessionStorage.getItem('vue-session-key') ? JSON.parse(sessionStorage.getItem('vue-session-key')).user.roleId : undefined;  
      if (isAdmin) {
        next();
      } else {
        next(from);
      }
    }
  } catch(err) {
    console.log('error in router', err);
  }
});

export default {
  name: "App",
  router
};
</script>

<style>
.group {
  position: relative;
  margin-bottom: 45px;
}

input {
  font-size: 18px;
  padding: 10px 10px 10px 5px;
  display: block;
  width: 100%;
  border: none;
  border-bottom: 1px solid #f5f5f5;
  color: #f5f5f5;
  background: #343a40;
}

input.error {
  border-bottom: 1px solid red;
}

input:focus {
  outline: none;
}

label.transform-label {
  color: #f5f5f5;
  font-size: 16px;
  font-weight: normal;
  position: absolute;
  pointer-events: none;
  left: 5px;
  top: 10px;
  transition: 0.2s ease all;
  -moz-transition: 0.2s ease all;
  -webkit-transition: 0.2s ease all;
}

label.error {
  color: rgb(220,53,69);
}

input:focus ~ label,
input:valid ~ label {
  top: -20px;
  font-size: 14px;
  color: #f5f5f5;
}

input.error:focus ~ label.error,
input.error:valid ~ label.error {
  top: -20px;
  font-size: 14px;
  color: rgb(220,53,69);
}


.bar {
  position: relative;
  display: block;
  width: 100%;
}

.bar:before,
.bar:after {
  content: "";
  height: 2px;
  width: 0;
  bottom: 1px;
  position: absolute;
  background: #f5f5f5;
  transition: 0.2s ease all;
  -moz-transition: 0.2s ease all;
  -webkit-transition: 0.2s ease all;
}

.bar:before {
  left: 50%;
}

.bar:after {
  right: 50%;
}

input:focus ~ .bar:before,
input:focus ~ .bar:after {
  width: 50%;
}

.highlight {
  position: absolute;
  height: 60%;
  width: 100px;
  top: 25%;
  left: 0;
  pointer-events: none;
  opacity: 0.5;
}

input:focus ~ .highlight {
  -webkit-animation: inputHighlighter 0.3s ease;
  -moz-animation: inputHighlighter 0.3s ease;
  animation: inputHighlighter 0.3s ease;
}

@-webkit-keyframes inputHighlighter {
  from {
    background: #f5f5f5;
  }
  to {
    width: 0;
    background: transparent;
  }
}
@-moz-keyframes inputHighlighter {
  from {
    background: #f5f5f5;
  }
  to {
    width: 0;
    background: transparent;
  }
}
@keyframes inputHighlighter {
  from {
    background: #f5f5f5;
  }
  to {
    width: 0;
    background: transparent;
  }
}

.mb-25 {
  margin-bottom: 25px;
}

.bg-none {
  background: none;
}

input:-webkit-autofill,
input:-webkit-autofill:hover,
input:-webkit-autofill:focus,
input:-webkit-autofill:active {
  transition: "color 9999s ease-out, background-color 9999s ease-out";
  transition-delay: 9999s;
}

.flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.cursor {
  cursor: pointer;
}
</style>

