<template>
  <div class="row">
    <div class="col-4 ">
      <FormulateForm class="login-form">
        <h2 class="form-title">Register</h2>

        <FormulateInput
          name="email"
          type="email"
          v-model="form.email"
          label="Email address"
          autocomplete="off"
          validation="required|email"
        />
        <label v-show="mode == 'ADD'" class="form-label" for="form3Example3c"
          >Your Password</label
        >
        <FormulateInput
          name="password"
          v-show="mode == 'ADD'"
          type="password"
          v-model="form.password"
          validation="required"
        />
        <label v-show="mode == 'ADD'" class="form-label" for="form3Example3c"
          >Confirm Password</label
        >
        <FormulateInput
          type="password"
          v-model="form.confirmation"
          v-show="mode == 'ADD'"
          validation="required|confirm"
        />
        <br />
        <button class="btn-primary btn-sm" @click="save">
          {{ mode }} DATA
        </button>
        <br /><br />
        <button
          class="btn-primary btn-sm"
          @click="reset"
          v-show="mode == 'EDIT'"
        >
          Reset
        </button>
      </FormulateForm>
    </div>
    <div class="col-md-8">
      <table border="2px">
        <tr>
          <th>Email</th>
          <th>Action</th>
        </tr>
        <tr v-for="item in users" :key="item.id">
          <td>{{ item.email }}</td>
          <td>
            <button class="btn-primary btn-sm" @click="editUser(item)">
              edit
            </button>
          </td>
          <td>
            <button class="btn-danger btn-sm" @click="deletUser(item)">
              delete
            </button>
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      form: {
        name: "",
        email: "",
        password: "",
        confirmation: "",
      },
      users: [],
      msg: "",
      mode: "ADD", // ADD and EDIT
    };
  },
  async created() {
    await this.getUsers();
    this.mode = "ADD";
  },
  watch: {
    someObject: {
      handler(newValue, oldValue) {},
      deep: true,
    },
  },
  methods: {
    async getUsers() {
      const axioObj = {
        method: "get",
        url: "/v1/users?page=1&per_page=10",
        headers: {
          "Content-type": "multipart/form-data",
          Authorization: "Bearer eb4a35d1-ca5d-487a-b16e-05598fe7a3bb",
        },
      };
      const res = await axios(axioObj);
      if (res) {
        this.users = res.data.items;
        console.log(this.users);
      }
    },
    async save() {
      if (this.mode == "ADD") {
        await this.postUser();
      } else {
        await this.putUser();
      }
      await this.getUsers();
      this.reset();
    },
    async postUser() {
      try {
        var formData = new FormData();
        formData.append("email", this.form.email);
        formData.append("password", this.form.password);
        formData.append("confirmation", this.form.confirmation);
        const axioObj = {
          method: "post",
          url: "/v1/users",
          data: formData,
          headers: {
            "Content-type": "multipart/form-data",
            Authorization: "Bearer eb4a35d1-ca5d-487a-b16e-05598fe7a3bb",
          },
        };
        await axios(axioObj);
      } catch (e) {
        this.msg = e.response.data.error;
      }
    },
    async putUser() {
      var formData = new FormData();
      formData.append("email", this.form.email);
      formData.append("custom_data", JSON.stringify({}));
      const axioObj = {
        method: "POST",
        url: "/v1/users/" + this.form.id,
        data: formData,
        headers: {
          "Content-type": "multipart/form-data",
          Authorization: "Bearer eb4a35d1-ca5d-487a-b16e-05598fe7a3bb",
        },
      };
      await axios(axioObj);
    },
    async deletUser(i) {
      const axioObj = {
        method: "delete",
        url: "/v1/users/" + i.id,
        headers: {
          "Content-type": "multipart/form-data",
          Authorization: "Bearer eb4a35d1-ca5d-487a-b16e-05598fe7a3bb",
        },
      };
      const res = await axios(axioObj);
      if (res) {
        await this.getUsers();
      }
    },
    editUser(user) {
      this.form.id = user.id;
      this.form.email = user.email;
      this.mode = "EDIT";
    },
    reset() {
      this.mode = "ADD";
      this.form = {};
    },
  },
};
</script>
<style scoped>
.login-form {
  padding: 1em;
  border: 2px solid blue;
  border-radius: 0.5em;
  max-width: 500px;
  box-sizing: border-box;
}
.form-title {
  margin-top: 0;
  box-sizing: border-box;
}
.login-form::v-deep .formulate-input .formulate-input-element {
  max-width: none;
}
@media (min-width: 420px) {
  .double-wide {
    display: flex;
  }
  .double-wide .formulate-input {
    flex-grow: 1;
    width: calc(40% - 1em);
  }
  .double-wide .formulate-input:first-child {
    margin-right: 0.5em;
  }
  .double-wide .formulate-input:last-child {
    margin-left: 0.5em;
  }
}
</style>