<template>
  <v-container>
    <!-- Button to Add New To-Do -->
    <v-row>
      <v-col cols="12">
        <v-btn color="primary" @click="showAddTaskDialog">Add To-Do</v-btn>
      </v-col>
    </v-row>

    <!-- Dialog for Adding and Editing To-Do -->
    <v-dialog v-model="dialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">{{
            editing ? "Edit To-Do" : "Create To-Do"
          }}</span>
        </v-card-title>
        <v-card-text>
          <v-text-field
            v-model="taskSubject"
            label="Subject"
            :rules="[rules.required]"
          />
          <v-textarea
            v-model="taskDescription"
            label="Description"
            :rules="[rules.required]"
          />
        </v-card-text>
        <v-card-actions>
          <v-btn color="blue darken-1" text @click="closeDialog">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="saveTask">{{
            editing ? "Save" : "Add"
          }}</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- List of Tasks -->
    <v-row v-if="tasks.length > 0" class="mt-4">
      <v-col v-for="task in tasks" :key="task.id" cols="12" md="6">
        <v-card class="pa-4">
          <v-card-title>
            <span class="headline"
              >{{ task.activities_no }}: {{ task.subject }}</span
            >
          </v-card-title>
          <v-card-subtitle>{{ task.description }}</v-card-subtitle>

          <!-- Status tag below description -->
          <v-chip
            v-if="task.status"
            color="primary"
            text-color="white"
            class="mt-2"
          >
            {{ task.status }}
          </v-chip>

          <!-- Action Buttons below status -->
          <v-row class="mt-3">
            <v-col cols="12">
              <v-btn
                v-if="task.status === 'unmarked'"
                @click="markTask(task, 'done')"
                color="green"
                block
              >
                Mark as Done
              </v-btn>
              <v-btn
                v-if="task.status === 'unmarked'"
                @click="markTask(task, 'canceled')"
                color="red"
                block
              >
                Mark as Canceled
              </v-btn>
              <v-btn
                v-if="task.status !== 'unmarked'"
                @click="markTask(task, 'unmarked')"
                color="yellow"
                block
              >
                Unmark
              </v-btn>
              <v-btn
                v-if="task.status === 'unmarked'"
                @click="editTask(task)"
                color="blue"
                block
              >
                Edit
              </v-btn>
              <v-btn
                v-if="task.status === 'unmarked'"
                @click="deleteTask(task)"
                color="purple"
                block
              >
                Delete
              </v-btn>
            </v-col>
          </v-row>
        </v-card>
      </v-col>
    </v-row>

    <!-- If No Tasks -->
    <v-alert v-else type="info" class="mt-4">No To-Do activities yet!</v-alert>
  </v-container>
</template>

<script>
export default {
  name: "HomePage",
  data() {
    return {
      dialog: false,
      editing: false,
      taskSubject: "",
      taskDescription: "",
      taskToEdit: null,
      tasks: [],
      rules: {
        required: (value) => !!value || "This field is required",
      },
    };
  },
  methods: {
    showAddTaskDialog() {
      this.taskSubject = "";
      this.taskDescription = "";
      this.editing = false;
      this.dialog = true;
    },
    closeDialog() {
      this.dialog = false;
    },
    saveTask() {
      if (this.editing) {
        this.taskToEdit.subject = this.taskSubject;
        this.taskToEdit.description = this.taskDescription;
      } else {
        this.tasks.push({
          id: Date.now(),
          activities_no: `AC-${String(this.tasks.length + 1).padStart(4, "0")}`,
          subject: this.taskSubject,
          description: this.taskDescription,
          status: "unmarked", // default status
        });
      }
      this.closeDialog();
    },
    editTask(task) {
      this.taskSubject = task.subject;
      this.taskDescription = task.description;
      this.editing = true;
      this.taskToEdit = task;
      this.dialog = true;
    },
    markTask(task, status) {
      task.status = status;
    },
    deleteTask(task) {
      this.tasks = this.tasks.filter((t) => t.id !== task.id);
    },
  },
};
</script>

<style scoped>
/* Custom Styles */
.v-btn {
  margin-bottom: 8px;
}
</style>
