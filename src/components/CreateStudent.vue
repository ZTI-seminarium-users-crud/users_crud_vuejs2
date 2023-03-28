<template>
    <b-form class="mt-1">
      <b-row>
        <b-col cols="6">
          <b-form-group id="first-name" label="First Name" label-for="first-name">
            <b-form-input
              id="first-name"
              type="text"
              placeholder="First Name"
              v-model="student.first_name"
            ></b-form-input>
          </b-form-group>
        </b-col>
        <b-col cols="6">
          <b-form-group id="last-name" label="Last Name" label-for="last-name">
            <b-form-input
              id="last-name"
              type="text"
              placeholder="Last Name"
              v-model="student.last_name"
            ></b-form-input>
          </b-form-group>
        </b-col>
        <b-col cols="6">
          <b-form-group id="specialization" label="Specialization" label-for="specialization">
            <b-form-select
            id="inline-form-custom-select-pref"
            class="mb-2 mr-sm-2 mb-sm-0"
            :options="[{ text: 'Choose...', value: null }, 'Biology', 'Chemistry', 'Computer Science', 'Economics', 'Engineering', 'English', 'History', 'Mathematics', 'Physics', 'Political Science', 'Psychology']"
            :value="null"
            v-model="student.specialization"
            ></b-form-select>
          </b-form-group>
        </b-col>
        <b-col cols="3">
          <b-form-group id="gender" label="Gender" label-for="gender">
            <b-form-select
            id="inline-form-custom-select-pref"
            class="mb-2 mr-sm-2 mb-sm-0"
            :options="[{ text: 'Choose...', value: null }, 'Male', 'Female']"
            :value="null"
            v-model="student.gender"
            ></b-form-select>
          </b-form-group>
        </b-col>
        <b-col cols="3">
          <b-form-group id="degree" label="Degree" label-for="degree">
            <b-form-select
            id="inline-form-custom-select-pref"
            class="mb-2 mr-sm-2 mb-sm-0"
            :options="[{ text: 'Choose...', value: null }, 'Master', 'Bachelor', 'PhD']"
            :value="null"
            v-model="student.degree"
            ></b-form-select>
          </b-form-group>
        </b-col>
        <b-col cols="2">
          <b-form-group id="semester" label="Semester" label-for="semester">
            <b-form-select
            id="inline-form-custom-select-pref"
            class="mb-2 mr-sm-2 mb-sm-0"
            :options="[{ text: 'Choose...', value: null }, '1', '2', '3', '4', '5', '6', '7', '8']"
            :value="null"
            v-model="student.semester"
            ></b-form-select>
          </b-form-group>
        </b-col>
        <b-col cols="2">
          <b-form-group id="average_grade" label="Average grade" label-for="average_grade">
            <b-form-input
              id="average_grade"
              type="number"
              placeholder="Average grade"
              v-model="student.average_grade"
            ></b-form-input>
          </b-form-group>
        </b-col>
        <b-col cols="2">
          <b-form-group id="hair_color" label="Hair color" label-for="hair_color">
            <b-form-input
              id="hair_color"
              type="text"
              placeholder="Hair color"
              v-model="student.hair_color"
            ></b-form-input>
          </b-form-group>
        </b-col>
        <b-col cols="2">
          <b-form-group id="height" label="Height" label-for="height">
            <b-form-input
              id="height"
              type="number"
              placeholder="Height"
              v-model="student.height"
            ></b-form-input>
          </b-form-group>
        </b-col>
        <b-col cols="2">
          <b-form-group id="weight" label="Weight" label-for="weight">
            <b-form-input
              id="weight"
              type="number"
              placeholder="Weight"
              v-model="student.weight"
            ></b-form-input>
          </b-form-group>
        </b-col>
        <b-col cols="2">
          <b-form-group id="age" label="Age" label-for="age">
            <b-form-input
              id="age"
              type="text"
              placeholder="Age"
              v-model="student.age"
              aria-required="true" 
            ></b-form-input>
          </b-form-group>
        </b-col>
      </b-row>
      <b-row class="mt-4">
        <b-col cols="3">
          <b-button variant="primary" class="px-5" @click="addNewStudent"
            >Add Student</b-button
          >
        </b-col>
      </b-row>
    </b-form>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    name: "CreateStudentModal",
    data() {
      return {
        student: {},
      };
    },
    methods: {
      triggerClose() {
        this.$emit("closeCreateModal");
      },
      addNewStudent() {
        axios
          .post("http://localhost:3000/students/", this.student)
          .then((response) => {
            console.log(response.data);
            this.$emit("closeCreateModal");
            this.$emit("reloadDataTable");
            this.$emit("showSuccessAlert");
          })
          .catch((error) => {
            console.log(error);
          });
      },
    },
  };
  </script>
  