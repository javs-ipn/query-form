<template>
<div>
  <navbar></navbar>
  <div class="query-container bg-light flex-center">
      <form class="query-form bg-dark" @submit.prevent="sendQuery">
        <h1 class="text-center c-white mb-25 header-permissions">Permissions</h1>
        <div class="group mb-25">
          <input type="text" class="bg-none" autocomplete="off" required v-model="query"/>
          <span class="highlight"></span>
          <span class="bar"></span>
          <label class="transform-label">Query</label>
        </div>
        <div class="form-group">
          <select class="custom-select bg-light" id="inputGroupSelect01" v-model="bdId" required>
            <option selected disabled value="">--Select database--</option>
            <option :value="db.id" v-for="db of dbs" :key="db.id">{{ db.base }}</option>
          </select>
        </div>
        <div class="form-group">
          <label class="c-white" for="exampleFormControlTextarea1">Write a message</label>
          <textarea class="form-control" id="exampleFormControlTextarea1" rows="3" v-model="comments" required></textarea>
        </div>
        <div class="form-group button-container">
          <input type="submit" value="Submit" class="btn btn-danger btn-sigin"/>
            <div style="height: 37px;" class="custom-alert">
              <transition name="query-alert">
                <div class="alert alert-success mt-1" role="alert" v-if="successQuery">
                  The query has been successfully sent!
                </div>
              </transition>
            </div>
        </div>
      </form>
    </div>
</div>
</template>
<script>
import axios from "axios";
import NavBar from "./Navbar";
const api = "http://localhost:3000/api";
export default {
  created() {
    axios
      .get(api + "/db")
      .then((db) => {
        this.dbs = db.data; 
        console.log(db);
      })
      .catch((error) => {
        console.log("error", error);
      });
  },
  data() {
    return {
      query: '',
      comments: '',
      bdId: '',
      userId: 0,
      statusId: 0,
      successQuery: false,
      dbs: []
    };
  },
  components: {
    navbar: NavBar
  },
  methods: {
    sendQuery() {
      const user = this.$session.get('user');
      user.id;
      const queryToSend = {
        query: this.query,
        comments: this.comments,
        bdId: this.bdId,
        userId: user.id,
        statusId: 0,
        rejectMssg: ''
      };

      axios
        .post(api + "/query-form", queryToSend)
        .then(() => {
          this.successQuery = true;
          setTimeout(()=>{
            this.successQuery = false;
          }, 2000);
          this.query = '';
          this.comments = '';
          this.bdId =  '';
        })
        .catch((error) => {
          console.log("error", error);
        });
    }
  }
}
</script> 

<style>
textarea {
  resize: none;
}
.query-container {
  height: 90vh;
  padding-bottom: 5%;
}

.query-form {
  border-radius: 12px;
  width: 45%;
  padding: 2% 3%;
  margin-top: 5%;
}

.c-white {
  color: #f8f9fa;
}

@media (max-width: 920px) {
  .query-form {
    width: 50%;
    padding: 1% 4%;
  }
}
@media (max-width: 768px) {
  .query-form {
    width: 58%;
    padding: 4%;
  }
}

@media (max-width: 620px) {
  .header-permissions {
    font-size: 2em;
  }

  .query-form {
    width: 60%;
    padding: 4% 6%;
  }
}

@media (max-width: 520px) {
  .header-permissions {
    font-size: 1.5em;
  }
  .query-form {
    width: 65%;
  }
}


@media (max-width: 520px) {
  .query-form {
    width: 70%;
  }
}

.button-container {
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: flex-end;
}

.custom-alert {
  width: 100%;
  margin-bottom: 0;
}

.mt-1 {
  margin-top: 1em !important;
}

.query-alert-enter-active, .query-alert-leave-active {
  transition: opacity 1s;
}
.query-alert-enter, .query-alert-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>