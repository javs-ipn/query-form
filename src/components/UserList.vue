<template>
  <div>
    <navbar></navbar>
    <div class="queries-form">
    <h1 class="text-center queries-header table-responsive">User list</h1>
    <div class="queries-container">
      <table class="table table-responsive">
      <thead class="thead-dark">
        <tr>
          <th scope="col" class="w-25 text-center">User</th>
          <th scope="col" class="w-25 text-center">Email</th>
          <th scope="col" class="w-10 text-center">Role</th>
          <th scope="col" class="w-10 text-center">Status</th>
          <th scope="col" class="w-10 text-center"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(user, index) of users" :key="index" class="custom-tr">
          <td class="text-center">
            <span v-if="user.editing">
              <input type="text" class="form-control text-center" v-model="updateUser.user"  key="user1">
            </span>
            <input type="text" class="form-control empty-input" v-model="user.user" key="user2" disabled v-else>
          </td>
          <td class="text-center">
            <template v-if="user.editing">
              <input type="email" class="form-control text-center" v-model="updateUser.email" key="email1">
            </template>
            <input type="text" class="form-control empty-input" v-model="user.email" key="email2" disabled v-else>
          </td>
          <td class="text-center">
            <template v-if="user.editing">
              <select class="custom-select bg-light" id="inputGroupSelect01" v-model.number="updateUser.roleId">
                <option disabled value="">--Select role--</option>
                <option :value="role.id" v-for="role of roles" :key="role.id">{{ role.role }}</option>
              </select>
            </template>
            <input type="text" class="form-control empty-input" :value="getRole(user.roleId)" disabled v-else>
          </td>
          <td class="text-center">
            <template v-if="user.editing">
              <select class="custom-select bg-light text-center" id="inputGroupSelect01" v-model.number="updateUser.statusId">
                <option disabled value="">--Select status--</option>
                <option v-for="stat in status" :key="stat.id" :value="stat.id">{{ stat.status }}</option>
              </select>
            </template>
            <input type="text" class="form-control empty-input" :value="getStatus(user.statusId)"  disabled v-else>
          </td>
          <td class="text-center">
            <template v-if="user.editing">
              <img src="../assets/accept.svg" alt="Accept" class="accept-icon" title="Update user"
              @click="updateUserMethod(user)">
              <img src="../assets/reject.svg" alt="Reject" class="reject-icon" title="Cancel update"
              data-toggle="modal" data-target="#exampleModal" @click="cancelUpdate(user)">
            </template>
            <template v-else>
              <button class="btn btn-primary"  @click="editUser(user)">Edit user</button>
            </template>
          </td>
        </tr>
      </tbody>
    </table>
    </div>
    <create-user @createUser="createUser"></create-user>
  </div>
  </div>
</template>

<script>
import CreateUser from "./CreateUser.vue";
import NavBar from "./Navbar";
import axios from "axios";
const api = "http://localhost:3000/api";

export default {
  created() {
    axios
      .get(api + "/user")
      .then((users) => {
        users.data.forEach(user => {
          this.$set(user, 'editing', false);
        });
        this.users = users.data; 
        return axios
          .get(api + "/status")
          .then((status) => {
            status.data = status.data.filter(status => {
              return status.status != "Rechazado";
            });
            console.log('object', status.data);
            this.status = status.data; 
            return axios
              .get(api + "/role")
              .then((roles) => {
                this.roles = roles.data; 
                return axios
              })
          })
      })
      .catch((error) => {
        console.log("error", error);
      });
  },
  data() {
    return {
      users: [],
      status: [],
      roles: [],
      updateUser: {
        user: '',
        email: '',
        statusId: 0,
        roleId: 0
      }
    };
  },
  components: {
    "create-user": CreateUser,
    navbar: NavBar
  },
  methods: {
    createUser(new_user) {
      axios
      .post(api + "/user/createUser", new_user)
      .then(() => {
         this.users.push(new_user);
      })
      .catch((error) => {
        console.log("error", error);
      });
    },
    getStatus(roleId) {
      const filteredStatus = this.status.filter(stat => stat.id == roleId)[0];
      if (filteredStatus.status == 'Inactivo') {
        return 'Inactivo';
      } else if (filteredStatus.status == 'Activo') {
        return 'Activo';
      } else if(filteredStatus.status == 'Rechazado') {
        return 'Rechazado';
      }
      return filteredStatus.status;
    },
    getRole(roleId) {
      const filteredRole = this.roles.filter(role => role.id == roleId)[0];
      return filteredRole.role;
    },
    editUser(user) {
      this.exitEditing();
      this.updateUser = {
        user: user.user,
        email: user.email,
        statusId: user.statusId,
        roleId: user.roleId
      };
      user.editing = true;
    },
    exitEditing() {
      this.users.forEach(user => {
        user.editing = false;
      });
    },
    cancelUpdate(user) {
      user.editing = false;
    },
    updateUserMethod(user) {
      user.user = this.updateUser.user;
      user.email = this.updateUser.email;
      user.statusId = this.updateUser.statusId;
      user.roleId = this.updateUser.roleId;
      axios
      .post(api + "/user/updateUser", user)
      .then((updateUser) => {
        console.log('updateUser', updateUser);
        user.editing = false;
      })
      .catch((error) => {
        console.log("error", error);
      }); 
    } 
  }
};
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

.w-20 {
  width: 20%;
}

.w-40 {
  width: 40%;
}

.queries-header {
  margin-bottom: 18px;
  overflow: hidden;
}

.empty-input {
  text-align: center;
  border: none;
  outline: none;
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

.custom-tr {
  height: 63px;
  min-height: 63px;
  max-height: 63px;
}

.form-control:disabled {
  background: #FFF !important;
}
</style>