<template>
<div>
  <navbar></navbar>
  <div class="queries-form table-responsive">
    <h1 class="text-center queries-header">{{ user.user }} queries</h1>
    <table class="table table-hover">
      <thead class="thead-dark">
        <tr>
          <th scope="col" class="w-10 text-center">Database</th>
          <th scope="col" class="w-40 text-center">Query</th>
          <th scope="col" class="w-25 text-center">Comments</th>
          <th scope="col" class="w-10 text-center">Status</th>
          <th scope="col" class="w-15 text-center">Response</th>
        </tr>
      </thead>
      <paginate name="queries" :list="queries" :per="8" tag="tbody" v-if="queries.length != 0">
        <tr v-for="query in paginated('queries')" :key="query.id" :class="{'reject-query': getStatus(query) == 'Rechazado'}">
          <td class="text-center">{{ getDB(query) }}</td>
          <td class="text-center">{{ query.query }}</td>
          <td class="text-center">{{ query.comments }}</td>
          <td class="text-center">{{ getStatus(query) }}</td>
          <td class="text-center">{{ query.reject_mssg }}</td>
        </tr>
      </paginate>
    <paginate-links for="queries" :classes="{'ul': 'pagination', 'li': 'page-item', 'a': 'page-link'}" style="justify-content: flex-end;"></paginate-links>
    </table>
  </div>
</div>
</template>

<script>
import axios from "axios";
import NavBar from "./Navbar";
import VuePaginate from 'vue-paginate'
import Vue from 'vue'
Vue.use(VuePaginate)

const api = "http://localhost:3000/api";
export default {
  created() {
    const user = this.$session.get('user');
    const token = this.$session.get('token');
    const headers = {
      headers: { Authorization: `Bearer ${token}` }
    };
    axios
      .get(api + "/query-form/" + user.id, headers)
      .then((queries) => {
        this.queries = queries.data; 
        return axios
          .get(api + "/db", headers)
          .then((db) => {
            this.dbs = db.data; 
            return axios
              .get(api + "/status", headers)
              .then((status) => {
                this.status = status.data; 
              })
          })
      })
      .catch((error) => {
        console.log("error", error);
      });
  },
  data() {
    return {
      user: this.$session.get('user'),
      queries: [],
      dbs: [],
      status: [],
      paginate: ['queries']
    }
  },
  components: {
    navbar: NavBar
  },
  methods: {
    getStatus(query) {
      if (this.status.length !== 0) {
        const filteredStatus = this.status.filter(stat => stat.id == query.statusId)[0];
        if (filteredStatus.status == 'Inactivo') {
          return 'Pendiente';
        } else if (filteredStatus.status == 'Activo') {
          return 'Aceptado';
        } else if(filteredStatus.status == 'Rechazado') {
          return 'Rechazado';
        }
        return filteredStatus.status;
      }
    },
    getDB(query) {
      if (this.dbs.length !== 0) {
        const filteredDB = this.dbs.filter(db => db.id == query.bdId)[0];
        return filteredDB.base;
      }
    }
  }
}
</script>

<style scoped>
.queries-form {
  padding: 1% 1%;
}

.reject-query {
  background: rgb(220,53,69,0.7);
}

.w-10 {
  width: 10%;
}

.w-15 {
  width: 15%;
}

.w-25 {
  width: 25%;
}

.w-40 {
  width: 40%;
}

.queries-header {
  margin-bottom: 18px;
}
</style>