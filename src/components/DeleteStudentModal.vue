<template>
    <div id="delete-modal">
      <b-row class="mt-2 mb-3">
        <h3 class="text-danger">
          Are you sure?
        </h3>
      </b-row>
      <b-row class="mt-2 mb-3">

      </b-row>
      <b-row class="mt-4">
        <b-col>
          <b-button variant="danger" @click="removeStudentFromData"
            >Delete Student</b-button
          >
        </b-col>
      </b-row>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    name: "DeleteStudentModal",
    props: {
      studentId: Number,
    },
    methods: {
      triggerClose() {
        this.$emit("closeDeleteModal");
      },
      removeStudentFromData() {
        axios
          .delete(`http://localhost:3000/students/${this.studentId}`)
          .then(() => {
            this.$emit("reloadDataTable");
            this.$emit("showDeleteAlert");
            this.$emit("closeDeleteModal");
          })
          .catch((error) => {
            console.log(error);
          });
      },
    },
  };
  </script>
  <style>
#delete-modal {
  padding: 20px;
}
</style>