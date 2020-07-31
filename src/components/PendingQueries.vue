<template>
  <div>
    <navbar></navbar>
    <div class="queries-form">
      <h1 class="text-center queries-header table-responsive">Pending queries</h1>
      <table class="table table-hover">
        <thead class="thead-dark">
          <tr>
            <th scope="col" class="w-10 text-center">User</th>
            <th scope="col" class="w-40 text-center">Data base</th>
            <th scope="col" class="w-20 text-center">Query</th>
            <th scope="col" class="w-10 text-center">Comments</th>
            <th scope="col" class="w-10 text-center">Status</th>
            <th scope="col" class="w-10 text-center">Response</th>
            <th scope="col" class="w-10 text-center"></th>
          </tr>
        </thead>
        <paginate name="queries" :list="queries" :per="8" tag="tbody" v-if="queries.length != 0">
          <tr v-for="query in paginated('queries')" :key="query.id">
            <td class="text-center">{{ getUser(query.userId) }}</td>
            <td class="text-center">{{ getDB(query) }}</td>
            <td class="text-center">{{ query.query }}</td>
            <td class="text-center">{{ query.comments }}</td>
            <th scope="col" class="w-10 text-center" 
            :class="{'text-success': getStatus(query.statusId) == 'Aceptada', 'text-danger': getStatus(query.statusId) == 'Rechazada'}">{{ getStatus(query.statusId) }}</th>
            <td class="text-center">{{ query.rejectMssg }}</td>
            <td class="text-center">
              <div v-if="(!(getStatus(query.statusId) == 'Aceptada') && !(getStatus(query.statusId) == 'Rechazada'))">
                <img src="../assets/accept.svg" alt="Accept" class="accept-icon" title="Accept"
                @click="acceptRequest(query)">
                <img src="../assets/reject.svg" alt="Reject" class="reject-icon" title="Reject"
                data-toggle="modal" data-target="#exampleModal" @click="setQuery(query)">
              </div>
            </td>
          </tr>
        </paginate>
      </table>
      <paginate-links for="queries" :classes="{'ul': 'pagination', 'li': 'page-item', 'a': 'page-link'}" style="justify-content: flex-end;"></paginate-links>
    </div>
    <!-- Modal -->
    <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">Reject reason</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <textarea class="form-control" id="exampleFormControlTextarea1" rows="3" v-model="rejectMssg" required></textarea>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
            <button type="button" class="btn btn-danger" data-dismiss="modal" @click="rejectRequest">Reject request</button>
          </div>
        </div>
      </div>
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
const session = JSON.parse(sessionStorage.getItem('vue-session-key'));
const token = session ? session.token : undefined;
const headers = {
  headers: { Authorization: `Bearer ${token}` }
};
export default {
  created() {
    axios
      .get(api + "/queries", headers)
      .then((queries) => {
        this.queries = queries.data; 
        console.log('queries', this.queries);
        return axios
          .get(api + "/db", headers)
          .then((db) => {
            this.dbs = db.data; 
            return axios
              .get(api + "/status", headers)
              .then((status) => {
                this.status = status.data; 
                return axios
              .get(api + "/user", headers)
              .then((users) => {
                this.users = users.data; 
                return 
              }) 
              })
          })
      })
      .catch((error) => {
        console.log("error", error);
      });
  },
  data() {
    return {
      queries: [],
      status: [],
      dbs: [],
      users: [],
      paginate: ['queries'],
      rejectMssg: '',
      rejectQuery: undefined
    };
  },
  methods: {
    getDB(query) {
      const filteredDB = this.dbs.filter(db => db.id == query.bdId)[0];
      return filteredDB.base;
    },
    getUser(userId) {
      const filteredUser = this.users.filter(db => db.id == userId)[0];
      return filteredUser.user;
    },
    acceptRequest(query) {
      const queryInfo = {
          id: query.id,
          newStatus: 1,
          rejectMssg: ''
        };
        axios
          .post(api + "/query-form/updateQuery", queryInfo, headers)
          .then((queryUpdated) => {
            if (queryUpdated.data) {
              query.statusId = 1;
              query.rejectMssg = '';         
            } else {
              alert('There was an error running the query');
            }
          }) 
          .catch((error)=>{
            console.log(error);
          });
    },
    rejectRequest() {
      if (this.rejectQuery) {
        const queryInfo = {
          id: this.rejectQuery.id,
          newStatus: 2,
          rejectMssg: this.rejectMssg
        };
        axios
          .post(api + "/query-form/updateQuery", queryInfo, headers)
          .then(() => {
            this.rejectQuery.rejectMssg = this.rejectMssg;
            this.rejectQuery.statusId = 2;           
          }) 
          .catch((error)=>{
            console.log(error);
          });
      }
    },
    getStatus(statusId) {
      if (this.status.length != 0) {
        const filteredStatus = this.status.filter(stat => stat.id == statusId)[0];
        console.log('filteredStatus', statusId);
        if (filteredStatus.status == 'Inactivo') {
          return 'Pendiente';
        } else if (filteredStatus.status == 'Activo') {
          return 'Aceptada';
        } else if(filteredStatus.status == 'Rechazado') {
          return 'Rechazada';
        }
        return filteredStatus.status;
      }
    },
    setQuery(query) {
      this.rejectQuery = query;
    }
  },
  components: {
    navbar: NavBar
  }
}
</script>

<style scoped>
.queries-form {
  padding: 1% 1%;
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
  overflow: hidden;
}

.accept-icon {
  width: 20px;
  cursor: pointer;
}

.reject-icon {
  width: 17px;
  margin-left: 7px;
  cursor: pointer;
}
</style>