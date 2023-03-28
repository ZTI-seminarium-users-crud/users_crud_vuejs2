<template>
<b-row id="dataTable-row">
    <b-col sm="2" id="datatable-sidebar">


  <div v-for="category in filters">
    <hr/>

    <b-form-group>
      <template #label>
        <b>{{ category.title }}:</b>
      </template>

      <template v-slot="{ ariaDescribedby }">
        <b-form-checkbox-group
          :id="category.title"
          v-model="category.selected"
          :options="category.values"
          :aria-describedby="ariaDescribedby"
          :name="category.title"
          class="ml-4"
          :aria-label="category.title"
          @change="applyFilterToData"
          stacked
        ></b-form-checkbox-group>
      </template>
    </b-form-group>



</div>
<hr/>




    </b-col>
    <b-col sm="9">
        <div class="w-100">
      <b-row class="w-100">
        <b-alert class="w-100" v-model="showSuccessAlert" variant="success" dismissible>
          {{ alertMessage }}
        </b-alert>
      </b-row>
      <b-row>


        
        <div>



       <!-- <b-form-group
      label="Inline switch style checkboxes"
      v-slot="{ ariaDescribedby }"
    >
      <b-form-checkbox-group
        v-model="selected"
        :options="options"
        :aria-describedby="ariaDescribedby"
        size="lg"
        switches
      ></b-form-checkbox-group>
    </b-form-group> -->


  </div>

      </b-row>
      <b-row class="mt-3">
        <b-card class="w-100">
          <b-row align-h="between">
            <b-col cols="6">
              <h3>{{ tableHeader }}</h3>
            </b-col>
            <b-col cols="2">
              <b-row>
                <b-col>
                  <b-button
                    variant="dark"
                    id="show-btn"
                    @click="showCreateModal"
                  >
                    <b-icon-plus class="text-white"></b-icon-plus>
                    <span class="h6 text-white">Add student</span>
                  </b-button>
                </b-col>
              </b-row>
            </b-col>
          </b-row>
          <b-row class="mt-3">
            <b-table
              striped
              hover
              :items="items_filtered"
              :fields="fields"
              class="text-center"
            >
              <template #cell(contact_name)="data">
                {{
                  `${data.item.first_name} ${data.item.last_name}`
                }}
              </template>
              <template #cell(actions)="data">
                <b-row>
                  <b-col cols="7">
                    <b-icon-pencil-square
                      class="action-item"
                      variant="primary"
                      @click="getRowData(data.item.id)"
                    ></b-icon-pencil-square>
                  </b-col>
                  <b-col cols="1">
                    <b-icon-trash-fill
                      class="action-item"
                      variant="danger"
                      @click="showDeleteModal(data.item.id)"
                    ></b-icon-trash-fill>
                  </b-col>
                </b-row>
              </template>
            </b-table>
          </b-row>
        </b-card>
      </b-row>
  
      <!-- Modal for adding new students -->
      <b-modal
        ref="create-student-modal"
        size="xl"
        hide-footer
        title="Add new student to the database"
      >
        <create-student-form
          @closeCreateModal="closeCreateModal"
          @reloadDataTable="getStudentData"
          @showSuccessAlert="showAlertCreate"
        ></create-student-form>
      </b-modal>
  
      <!-- Modal for updating students -->
      <b-modal
        ref="edit-student-modal"
        size="xl"
        hide-footer
        title="Edit student information"
      >
        <edit-student-form
          @closeEditModal="closeEditModal"
          @reloadDataTable="getStudentData"
          @showSuccessAlert="showAlertUpdate"
          :studentId="studentId"
        ></edit-student-form>
      </b-modal>
  
      <!-- Delete Student Modal -->
      <b-modal
        ref="delete-student-modal"
        size="md"
        hide-footer
        title="Delete student from database"
      >
        <delete-student-modal
          @closeDeleteModal="closeDeleteModal"
          @reloadDataTable="getStudentData"
          @showDeleteAlert="showDeleteSuccessModal"
          :studentId="studentId"
        ></delete-student-modal>
      </b-modal>
    </div>
    </b-col>
</b-row>
  </template>
  
  <script>
  import axios from "axios";
  import CreateStudentForm from "@/components/CreateStudent.vue";
  import EditStudentForm from "@/components/EditStudentForm.vue";
  import DeleteStudentModal from "@/components/DeleteStudentModal.vue";
  
  let filter_data = [
    {
      title: "Specialization", 
      values: [ "Biology", "Chemistry", "Computer Science", "Economics", "Engineering", "English", "History", "Mathematics", "Physics", "Political Science", "Psychology" ],
      selected: [ "Biology", "Chemistry", "Computer Science", "Economics", "Engineering", "English", "History", "Mathematics", "Physics", "Political Science", "Psychology" ]
    },
    {
      title: "Degree", 
      values: [ "Master", "Bachelor", "PhD" ],
      selected: [ "Master", "Bachelor", "PhD" ]
    },
    {
      title: "Semester", 
      values: [ "1", "2", "3", "4", "5", "6", "7", "8" ],
      selected: [ "1", "2", "3", "4", "5", "6", "7", "8" ]
    }
  ]

  let table_fields = [
          {
            key: "id",
            label: "Id",
            sortable: true,
          },
          {
            key: "contact_name",
            label: "Name",
            sortable: true,
          },
          {
            key: "specialization",
            label: "Specialization",
            sortable: true,
          },
          {
            key: "gender",
            label: "Gender",
            sortable: true,
          },
          {
            key: "degree",
            label: "Degree",
            sortable: true,
          },
          {
            key: "semester",
            label: "Semester",
            sortable: true,
          },
          {
            key: "average_grade",
            label: "Avg grade",
            sortable: true,
          },
          {
            key: "hair_color",
            label: "Hair color",
            sortable: true,
          },
          {
            key: "height",
            label: "Height",
            sortable: true,
          },
          {
            key: "weight",
            label: "Weight",
            sortable: true,
          },
          {
            key: "age",
            label: "Age",
            sortable: true,
          },
          "actions",
]

  export default {
    components: {
      CreateStudentForm,
      EditStudentForm,
      DeleteStudentModal,
    },
    data() {
      return {
        // Note 'isActive' is left out and will not appear in the rendered table
        fields: table_fields,
        // selected: [], // Must be an array reference!
        // options: [
        //   { text: 'Id', value: 'orange' },
        //   { text: 'Name', value: 'apple' },
        //   { text: 'Specialization', value: 'pineapple' },
        //   { text: 'Gender', value: 'grape' }
        // ],
        items: [],
        items_filtered: [],
        numberOfStudents: 0,
        activeStudents: 0,
        activeStudentsData: [],
        studentId: 0,
        companySearchTerm: "",
        tableHeader: "",
        showSuccessAlert: false,
        alertMessage: "",
        filters: filter_data
      };
    },
    mounted() {
      this.getStudentData();
    },
    methods: {
      showCreateModal() {
        this.$refs["create-student-modal"].show();
      },
      closeCreateModal() {
        this.$refs["create-student-modal"].hide();
      },
      applyFilterToData() {
        this.items_filtered = [];
        for(var i=0; i<this.items.length; i++) {
            let specialization = this.items[i].specialization;
            let degree = this.items[i].degree;
            let semester = this.items[i].semester.toString();

            if(this.filters[0].selected.indexOf(specialization) === -1) {
                console.log("Skipping ", specialization);
                continue;
            }
            if(this.filters[1].selected.indexOf(degree) === -1) {
                continue;
            }
            if(this.filters[2].selected.indexOf(semester) === -1) {
                continue;
            }
            this.items_filtered.push(this.items[i]);
        }
        // console.log(this.items_filtered.length);
      },
      getStudentData() {
        axios
          .get("http://localhost:3000/students/")
          .then((response) => {
            this.tableHeader = "Students";
            this.items = response.data;
            this.numberOfStudents = response.data.length;
            this.activeStudentsData = response.data.filter(
              (item) => item.student_status === "active"
            );
            this.activeStudents = this.activeStudentsData.length;
            this.applyFilterToData();
          })
          .catch((error) => {
            console.log(error);
          });
      },
      getRowData(id) {
        this.$refs["edit-student-modal"].show();
        this.studentId = id;
      },
      closeEditModal() {
        this.$refs["edit-student-modal"].hide();
      },
      setFilterTotalIsActive() {
        this.tableHeader = "Total Students";
        this.getStudentData();
      },
      setFilterActiveIsActive() {
        this.tableHeader = "Active Students";
        this.items = this.activeStudentsData;
      },
      showAlertCreate() {
        this.showSuccessAlert = true;
        this.alertMessage = "Student was created successfully!";
      },
      showAlertUpdate() {
        this.showSuccessAlert = true;
        this.alertMessage = "Student was updated successfully";
      },
      showDeleteModal(id) {
        this.$refs["delete-student-modal"].show();
        this.studentId = id;
      },
      closeDeleteModal() {
        this.$refs["delete-student-modal"].hide();
      },
      showDeleteSuccessModal() {
        this.showSuccessAlert = true;
        this.alertMessage = "Student was deleted successfully!";
      },
    },
  };
  </script>
  
  <style>
  .action-item:hover {
    cursor: pointer;
  }

#datatable-sidebar {
   padding-left: 30px;
}

#dataTable-row {
    margin: 0px;
}

  </style>