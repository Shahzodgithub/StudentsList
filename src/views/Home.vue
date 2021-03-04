<template>
  <div class="home">
    <form
      @submit.prevent
      class="d-flex flex-column justify-content-center align-items-center"
    >
      <div class="row mb-3">
        <label for="name3" class="col-sm-2 col-form-label">Name</label>
        <div class="col-6">
          <input type="text" class="form-control" id="name3" v-model="name" />
        </div>
      </div>
      <div class="row mb-3">
        <label for="date3" class="col-sm-2 col-form-label">BirthDate</label>
        <div class="col-6 mr-1">
          <input
            type="date"
            class="form-control"
            id="date3"
            v-model="birthDate"
          />
        </div>
      </div>
      <button
        class="btn btn-width2 btn-primary m-auto"
        :disabled="name && birthDate ? false : true"
        @click="addStudent"
      >
        Add a Student
      </button>
    </form>

    <div class="dashboard-container">
      <div class="table-responsive">
        <table class="table caption-top">
          <caption style="font-size: 25px" class="text-center">
            Student's information
          </caption>
          <thead>
            <tr>
              <th scope="col-4">Name</th>
              <th scope="col-3">BirthDate</th>
              <th scope="col-3">delete/edit</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(student, index) in getLists" :key="index">
              <td class="col-4">
                <h4>{{ student.name }}</h4>
              </td>
              <td class="col-3">
                <p>{{ student.birthDate }}</p>
              </td>
              <td class="col-3 td-button">
                <button
                  class="btn btn-danger mb-2"
                  @click="removeStudent(index)"
                >
                  delete
                </button>
                <button
                  class="btn btn-success"
                  @click="isEditOpen = !isEditOpen"
                >
                  edit
                </button>
              </td>
              <div class="modalOutside" v-if="isEditOpen"></div>
              <div
                id="modalForm"
                v-if="isEditOpen"
                @submit.prevent
                class="d-flex flex-column justify-content-center align-items-start"
              >
                <div class="row mb-3">
                  <label for="name1" class="col-sm-2 col-form-label"
                    >Name</label
                  >
                  <div class="col-6">
                    <input
                      type="text"
                      class="form-control"
                      id="name1"
                      v-model="nameEdit"
                    />
                  </div>
                </div>
                <div class="row mb-3">
                  <label for="date1" class="col-sm-2 col-form-label"
                    >BirthDate</label
                  >
                  <div class="col-6">
                    <input
                      type="date"
                      class="form-control"
                      id="date1"
                      v-model="birthDateEdit"
                    />
                  </div>
                </div>
                <button
                  class="btn btn-width2 btn-primary m-auto"
                  @click="editStudent(index)"
                >
                  Change
                </button>
                <button
                  class="btn btn-width2 btn-secondary m-auto mt-1"
                  @click="reject"
                >
                  Reject
                </button>
              </div>

              <!-- <p v-if="getLists = []">Here is no any data yet</p> -->
            </tr>
          </tbody>
          <!-- <button class="btn btn-success btn-width2" @click="getStudents">
            Update
          </button> -->
        </table>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";

export default {
  name: "Home",
  data() {
    return {
      name: "",
      birthDate: "",
      nameEdit: "",
      birthDateEdit: "",
      getLists: [],
      isEditOpen: false,
    };
  },
  components: {},
  methods: {
    // isFilled(){
    //   if(this.name){
    //     return false
    //   }
    // },
    async addStudent() {
      await axios
        .post(
          "https://studentslist-fd361-default-rtdb.firebaseio.com/students.json",
          {
            name: this.name,
            birthDate: this.birthDate,
          }
        )
        .catch(function (error) {
          console.log(error);
        });
      this.name = "";
      this.birthDate = "";

      return axios
        .get(
          "https://studentslist-fd361-default-rtdb.firebaseio.com/students.json"
        )
        .then((response) => {
          this.getLists = response.data;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    // getStudents() {
    //   return axios
    //     .get(
    //       "https://studentslist-fd361-default-rtdb.firebaseio.com/students.json"
    //     )
    //     .then((response) => {
    //       this.getLists = response.data;
    //     })
    //     .catch(function (error) {
    //       console.log(error);
    //     });
    // },
    removeStudent(index) {
      axios
        .delete(
          "https://studentslist-fd361-default-rtdb.firebaseio.com/students/" +
            index +
            ".json"
        )
        .then((res) => {
          axios
            .get(
              "https://studentslist-fd361-default-rtdb.firebaseio.com/students.json"
            )
            .then((response) => {
              this.getLists = response.data;
            })
            .catch(function (error) {
              console.log(error);
            });
          console.log(res);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    editStudent(index) {
      axios
        .put(
          "https://studentslist-fd361-default-rtdb.firebaseio.com/students/" +
            index +
            ".json",
          { name: this.nameEdit, birthDate: this.birthDateEdit }
        )
        .then((res) => {
          this.getLists[index] = res.data;
        });

      this.isEditOpen = !this.isEditOpen;
    },
    reject() {
      this.isEditOpen = !this.isEditOpen;
    },
  },
};
</script>


<style lang="scss">
.dashboard-container {
  width: 800px;
  margin: 44px auto 0 auto !important;
}
.btn {
  display: block;
  width: 100px;
}
.mr-1 {
  margin-right: -24px;
}
.btn-width1 {
  width: 120px;
}
.btn-width2 {
  width: 140px;
}
.dashboard {
  &-container {
    margin: 30px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}
.td-button .btn {
  display: block;
  width: 100px;
  margin-left: 59px;
  padding: 0;
  text-transform: capitalize;
  font-size: 15px;
}
input.form-control {
  width: 500px;
}
th {
  font-size: 15px;
}
.table-responsive {
  margin-bottom: 30px;
  border-radius: 10px;
  padding: 20px;
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
}

#modalForm {
  padding: 20px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateX(-50%) translateY(-50%);
  background-color: burlywood;
  border: 1px solid #000;
}

.modalOutside {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.2) !important;
  height: 100%;
}
</style>