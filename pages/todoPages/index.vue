<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-btn color="primary" @click="showAddTaskDialog">Add To-Do</v-btn>
      </v-col>
    </v-row>

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
        </v-card-text>
        <v-card-actions>
          <v-btn text @click="closeDialog">Cancel</v-btn>
          <v-btn text @click="saveTask">
            {{ editing ? "Save" : "Add" }}
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-row v-if="tasks.length > 0" class="mt-4">
      <v-col v-for="task in tasks" :key="task.id" cols="12" md="6">
        <v-card class="pa-4">
          <v-card-title>
            <span class="headline">{{ task.id }}: {{ task.name }}</span>
          </v-card-title>

          <v-list v-if="task.items && task.items.length > 0" class="mt-2">
            <v-list-item
              v-for="item in task.items"
              :key="item.id"
              style="display: flex; justify-content: space-between"
            >
              <v-list-item-content>
                {{ item.name }}
              </v-list-item-content>
              <div style="display: flex; gap: 20px; margin-top: 10px">
                <v-btn
                  icon
                  color="orange"
                  @click="showRenameItemDialog(task.id, item.id)"
                  size="small"
                >
                  <v-icon>mdi-pencil</v-icon>
                </v-btn>

                <v-btn
                  icon
                  color="red"
                  @click="deleteItemFromChecklist(task.id, item.id)"
                  size="small"
                >
                  <v-icon>mdi-delete</v-icon>
                </v-btn>

                <v-btn
                  icon
                  :color="item.itemCompletionStatus ? 'green' : 'red'"
                  @click="
                    updateItemStatus(
                      task.id,
                      item.id,
                      item.itemCompletionStatus
                    )
                  "
                  size="small"
                >
                  <v-icon>{{
                    item.itemCompletionStatus
                      ? "mdi-checkbox-marked-circle-outline"
                      : "mdi-close-circle-outline"
                  }}</v-icon>
                </v-btn>
              </div>
            </v-list-item>
          </v-list>

          <v-btn color="green" block @click="showAddItemDialog(task.id)">
            Add Item
          </v-btn>
        </v-card>
      </v-col>
    </v-row>

    <v-alert v-else type="info" class="mt-4">No To-Do activities yet!</v-alert>

    <v-dialog v-model="itemDialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Add Item</span>
        </v-card-title>
        <v-card-text>
          <v-text-field
            v-model="newItemName"
            label="Item Name"
            :rules="[rules.required]"
          />
        </v-card-text>
        <v-card-actions>
          <v-btn text @click="closeItemDialog">Cancel</v-btn>
          <v-btn text @click="addItemToChecklist">Add</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="renameItemDialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Rename Item</span>
        </v-card-title>
        <v-card-text>
          <v-text-field
            v-model="newItemName"
            label="Item Name"
            :rules="[rules.required]"
          />
        </v-card-text>
        <v-card-actions>
          <v-btn text @click="closeRenameItemDialog">Cancel</v-btn>
          <v-btn text @click="renameItemInChecklist">Rename</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import Cookies from "js-cookie";
import axios from "axios";

export default {
  name: "HomePage",
  data() {
    return {
      dialog: false,
      itemDialog: false,
      renameItemDialog: false,
      editing: false,
      userAuthToken: "",
      taskSubject: "",
      newItemName: "",
      currentTaskId: null,
      currentItemId: null,
      taskToEdit: null,
      tasks: [],
      rules: {
        required: (value) => !!value || "This field is required",
      },
    };
  },
  created() {
    const authToken = Cookies.get("authToken");
    this.userAuthToken = authToken;
    if (!authToken) {
      this.$router.push("/");
    } else {
      this.fetchTasks();
    }
  },
  methods: {
    async fetchTasks() {
      try {
        const res = await axios.get("http://94.74.86.174:8080/api/checklist", {
          headers: { Authorization: `Bearer ${this.userAuthToken}` },
        });
        this.tasks = res.data.data;
      } catch (error) {
        console.error("Error fetching tasks:", error.response?.data || error);
      }
    },
    showAddTaskDialog() {
      this.taskSubject = "";
      this.editing = false;
      this.dialog = true;
    },
    closeDialog() {
      this.dialog = false;
    },
    async saveTask() {
      try {
        if (this.editing) {
          await axios.put(
            `http://94.74.86.174:8080/api/checklist/${this.taskToEdit.id}`,
            { name: this.taskSubject },
            { headers: { Authorization: `Bearer ${this.userAuthToken}` } }
          );
        } else {
          await axios.post(
            "http://94.74.86.174:8080/api/checklist",
            { name: this.taskSubject },
            { headers: { Authorization: `Bearer ${this.userAuthToken}` } }
          );
        }
        this.fetchTasks();
        this.closeDialog();
      } catch (error) {
        console.error("Error saving task:", error.response?.data || error);
      }
    },
    showAddItemDialog(taskId) {
      this.currentTaskId = taskId;
      this.newItemName = "";
      this.itemDialog = true;
    },
    closeItemDialog() {
      this.itemDialog = false;
    },
    async addItemToChecklist() {
      try {
        await axios.post(
          `http://94.74.86.174:8080/api/checklist/${this.currentTaskId}/item`,
          { itemName: this.newItemName },
          { headers: { Authorization: `Bearer ${this.userAuthToken}` } }
        );
        this.fetchTasks();
        this.closeItemDialog();
      } catch (error) {
        console.error("Error adding item:", error.response?.data || error);
      }
    },
    async deleteItemFromChecklist(taskId, itemId) {
      try {
        await axios.delete(
          `http://94.74.86.174:8080/api/checklist/${taskId}/item/${itemId}`,
          { headers: { Authorization: `Bearer ${this.userAuthToken}` } }
        );
        this.fetchTasks();
      } catch (error) {
        console.error("Error deleting item:", error.response?.data || error);
      }
    },
    showRenameItemDialog(taskId, itemId) {
      this.currentTaskId = taskId;
      this.currentItemId = itemId;
      this.renameItemDialog = true;
    },
    closeRenameItemDialog() {
      this.renameItemDialog = false;
    },
    async renameItemInChecklist() {
      try {
        await axios.put(
          `http://94.74.86.174:8080/api/checklist/${this.currentTaskId}/item/rename/${this.currentItemId}`,
          { itemName: this.newItemName },
          { headers: { Authorization: `Bearer ${this.userAuthToken}` } }
        );
        this.fetchTasks();
        this.closeRenameItemDialog();
      } catch (error) {
        console.error("Error renaming item:", error.response?.data || error);
      }
    },
    async updateItemStatus(taskId, itemId, currentStatus) {
      try {
        await axios.put(
          `http://94.74.86.174:8080/api/checklist/${taskId}/item/${itemId}`,
          { itemCompletionStatus: !currentStatus },
          { headers: { Authorization: `Bearer ${this.userAuthToken}` } }
        );
        this.fetchTasks();
      } catch (error) {
        console.error("Error updating status:", error.response?.data || error);
      }
    },
  },
};
</script>

<style scoped>
.v-btn {
  margin-bottom: 8px;
}
</style>
