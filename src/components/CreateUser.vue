<template>
  <div class="create-container">
    <form class="create-form bg-dark" @submit.prevent="createUser()">
      <div class="group mb-25">
        <input type="text" class="bg-none" autocomplete="off" required v-model="new_user.user" />
        <span class="highlight"></span>
        <span class="bar"></span>
        <label class="transform-label">Name<sup>*</sup></label>
      </div>
      <div class="group mb-25">
        <input type="email" class="bg-none" autocomplete="off" required v-model="new_user.email" />
        <span class="highlight"></span>
        <span class="bar"></span>
        <label class="transform-label">Email<sup>*</sup></label>
      </div>
      <div class="group mb-25">
        <input type="password" class="bg-none" autocomplete="off" required v-model="new_user.pass" />
        <span class="highlight"></span>
        <span class="bar"></span>
        <label class="transform-label">Password<sup>*</sup></label>
        <p style="margin: 0;color: rgb(220, 53, 69);max-width: 324px;" v-show="!validPassword()">
          Utiliza ocho caracteres como mínimo con una combinación de letras, números y símbolos
        </p>
      </div>
      <div class="form-group">
        <select class="custom-select bg-light" id="inputGroupSelect01" v-model="new_user.roleId">
          <option disabled>--Select role--</option>
          <option value="0">User</option>
          <option value="1">Administrator</option>
        </select>
      </div>
      <input type="submit" value="Create user" class="btn btn-danger btn-sigin w-10" :disabled="disabledForm()"/>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      new_user: {
        user: "",
        email: "",
        roleId: 0,
        pass: "",
        statusId: 1
      }
    };
  },
  methods: {
    createUser() {
      this.$emit("createUser", this.new_user);
      this.new_user = {
        user: "",
        email: "",
        roleId: 0,
        pass: "",
        statusId: 1
      };
    },
    disabledForm() {
      const regexPassword = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,10}$/;
      const regexEmail = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@(([[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      if (this.new_user.name === '' || this.new_user.email === '' || !regexEmail.test(this.new_user.email) || !regexPassword.test(this.new_user.pass)) {
        return true;
      }
    },
    validPassword() {
      const regexPassword = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,10}$/;
      return regexPassword.test(this.new_user.pass) || this.new_user.pass === '';
    }
  }
};
</script>

<style scoped>
.create-form {
  width: 95%;
  display: flex;
  align-items: baseline;
  justify-content: space-around;
  border-radius: 10px;
  padding: 2%;
  padding-bottom: 0;
  padding-right: 0;
  padding-left: 0;
}

.w-10 {
  width: 10%;
}

.create-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

@media (max-width: 1000px) {
  .create-form {
    flex-direction: column;
    align-items: center;
    width: 50%;
    padding: 3%;
  }

  .group, .form-group {
    width: 80%;
  }

  .btn-sigin {
    width: 80%;
  }
}

@media (max-width: 1000px) {
  .create-form {
    width: 65%;
  }
}
</style>