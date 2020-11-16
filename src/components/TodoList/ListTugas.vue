<template>
  <v-main class="list">
    <h3 class="text-h3 font-weight-medium mb-5">To Do List TUGAS</h3>
    <v-card>
      <v-card-title>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
        <v-spacer></v-spacer>
        <v-btn color="success" dark @click="dialog = true"> Tambah </v-btn>
      </v-card-title>
      <v-data-table
        :headers="headers"
        :items="todos"
        :search="search"
        :single-expand="singleExpand"
        :expanded.sync="expanded"
        v-model="selected"
        item-key="task"
        show-expand
        show-select
        class="elevation-1"
      >
       
        <template v-slot:[`item.priority`]="{ item }">
          <v-chip :color="getColor(item.priority)" label outlined>{{
            item.priority
          }}</v-chip>
        </template>
        <template v-slot:[`item.actions`]="{ item }">
          <v-btn small class="mr-2" @click="editItem(item)"> edit </v-btn>
          <v-btn small @click="deleteItem(item)"> delete </v-btn>
        </template>
        <template v-slot:expanded-item="{ headers, item }">
          <td :colspan="headers.length">
            {{ item.note }}
          </td>
        </template>
      </v-data-table>
    </v-card>

    <v-card v-if="selected.length"  width="100%" class="mt-5">
      <ul class="ml-5">
        <li v-for="(item,index) in selected" :key="index">
          {{item.task}}
        </li>
      </ul>
      <v-btn class="ml-5 mt-5 mb-5" color="error" @click="deleteAllItemSelected">DELETE</v-btn>
    </v-card>
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Form Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field
              v-model="formTodo.task"
              label="Task"
              required
            ></v-text-field>
            <v-select
              v-model="formTodo.priority"
              :items="['Penting', 'Biasa', 'Tidak penting']"
              label="Priority"
              required
            ></v-select>
            <v-textarea
              v-model="formTodo.note"
              label="Note"
              required
            ></v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel"> Cancel </v-btn>
          <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialog2" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Edit Todo</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field
              v-model="formTodo.task"
              label="Task"
              required
            ></v-text-field>
            <v-select
              v-model="formTodo.priority"
              :items="['Penting', 'Biasa', 'Tidak penting']"
              label="Priority"
              required
            ></v-select>
            <v-textarea
              v-model="formTodo.note"
              label="Note"
              required
            ></v-textarea>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel"> Cancel </v-btn>
          <v-btn color="blue darken-1" text @click="updateConfirmation">
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialog3" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline" style="text-align: center"
            >Yakin Ingin Menghapus?</span
          >
        </v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="cancel"> No </v-btn>
          <v-btn color="blue darken-1" text @click="deleteConfirmation">
            Yes
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-main>
</template>
<script>
export default {
  name: "List",
  data() {
    return {
      sortBy: null,
      expanded: [],
      selected: [],
      singleExpand: false,
      search: null,
      dialog2: false,
      dialog3: false,
      dialog: false,
      oldIndex: 0,
      sortKey: ["Penting", "Biasa", "Tidak penting"],
      sortKey2: ["Tidak penting", "Biasa", "Penting"],
      headers: [
        {
          text: "Task",
          align: "start",
          sortable: true,
          value: "task",
        },
        { text: "Priority", value: "priority" },
        { text: "Actions", value: "actions" },
        { text: '', value: "data-table-select",align:'center',width:'15%'}
      ],
      todos: [
        {
          task: "bernafas",
          priority: "Penting",
          note: "huffttt",
        },
        {
          task: "nongkrong",
          priority: "Tidak penting",
          note: "bersama tman2",
        },
        {
          task: "masak",
          priority: "Biasa",
          note: "masak air 500ml",
        },
      ],
      formTodo: {
        task: null,
        priority: null,
        note: null,
      },
      test: {
        task: null,
        priority: null,
        note: null,
      },
    };
  },
  methods: {
    save() {
      this.todos.push(this.formTodo);
      this.resetForm();
      this.dialog = false;
    },
    cancel() {
      this.resetForm();
      this.dialog = false;
      this.dialog2 = false;
      this.dialog3 = false;
    },
    resetForm() {
      this.formTodo = {
        task: null,
        priority: null,
        note: null,
      };
    },
    editItem(item) {
      this.dialog2 = true;
      this.oldIndex = this.todos.indexOf(item);
      this.formTodo = {
        task: item.task,
        priority: item.priority,
        note: item.note,
      };
    },
    deleteItem(item) {
      this.dialog3 = true;
      this.formTodo = item;
    },
    deleteConfirmation() {
      this.dialog3 = false;
      this.todos.splice(this.todos.indexOf(this.formTodo), 1);
    },
    updateConfirmation() {
      this.todos[this.oldIndex].task = this.formTodo.task;
      this.todos[this.oldIndex].priority = this.formTodo.priority;
      this.todos[this.oldIndex].note = this.formTodo.note;
      this.dialog2 = false;
      this.resetForm();
    },
    getColor(priority) {
      if (priority == "Penting") return "red";
      else if (priority == "Tidak penting") return "green";
      else return "blue";
    },
    deleteAllItemSelected(){
      for( var item in this.selected){
        var deleteIndex = this.todos.indexOf(this.selected[item]);
        this.todos.splice(deleteIndex,1);
      }

      this.selected = [];
    }
  },
};
</script>