<script setup>
    import { ref, onMounted } from 'vue'
    import { storeToRefs } from 'pinia'
    import { useStoreTasks } from '@/stores/storetasks'
    import axios from 'axios'

    const store = useStoreTasks()

    const {  tasks, totalTasks, status } = storeToRefs(store)
    
    const taks_input = ref('')

    const addtask = async () => {
        alert(totalTasks.value)
        const v = { "id": (Number(totalTasks) + 1), "task":taks_input.value, "status":"Ongoing", "favorite":0 }
        const res = await axios.post('http://127.0.0.1:3000/task/', v,
          {
            headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            }
          }
        );
        console.log(res);
    }

    const completed = async (id) => {
        let confirm_message = "Confirm to update task as completed"
        if (confirm(confirm_message) == false) {
            return false;
        }
        const v = { "status":"Completed" }
        const res = await axios.patch(`http://127.0.0.1:3000/task/${id}`, v,
          {
            headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            }
          }
        );
        console.log(res);
    }

    function TasksList(v) {
        status.value = v //Just in case other component need
        tasks.value = tasks.value.filter((task) => { return task.status === v })
    }

    const getData = async () => {
      const res = await axios.get("http://127.0.0.1:3000/task");
      tasks.value = res.data;
      console.log(res);
    }
    onMounted(getData);

</script>

<style>
    @import "bootstrap/dist/css/bootstrap.css";
    @import "bootstrap-icons/font/bootstrap-icons.css";
</style>

<template>
        <div class="center" style="width: 800px">    
<h1 style="text-align: center;">Vuejs with Pinia<br />(Task management)</h1><br />
<div class="center" style="width: 100%;">
    <div class="center" style="display: flex;">
        <input type="text" id="txtaddTask"  class="form-control" v-model="taks_input" 
        placeholder="Add task"
        style="width: 180px;" />
        <button @click="addtask" class="btn btn-primary" 
        style="margin-left: 10px; background-color: #0FEFEF;"
        >Add Task</button>
    </div>

    <div class="center">
        Total tasks: {{ totalTasks }}<br />
        <div style="margin-bottom: 8px;">
            <button @click="getData" class="btn btn-primary" 
            style="padding-left: 4px; margin-right: 4px;">
                All tasks
        </button>
        <button @click="TasksList('Ongoing')" class="btn btn-primary" 
            style="padding-left: 4px; margin-right: 4px;">
                Ongoing
        </button>
        <button @click="TasksList('Completed')" class="btn btn-primary" 
            style="padding-left: 4px; margin-right: 4px;">
                Completed
        </button>
        </div>
       
<div class="alltasks">
    <div class="tasks" v-for="task in tasks" style="padding: 8px; width: 80%; border: 1px solid gray;" >
        <table>
            <tr>
                <td><div id="iddiv">{{ task.id }}</div></td>
                <td>{{ task.task }}</td>
                <td><i v-show="task.status === 'Completed'" class="bi bi-check-circle-fill" style="font-size: 24px; color: #EC7063;"></i></td>
                <td>
                    <button v-show="task.status === 'Ongoing'" @click="completed(task.id)">Mark as completed</button>
                </td>
            </tr>
        </table>      
    </div> 
</div>
    </div>
</div>
</div>

</template>

<style scoped>
.center {
  margin: auto;
  width: 50%;
  padding: 10px;
}
#iddiv {
    width: 50px; 
    height: 50px; 
    text-align: center; 
    border-radius: 50%; 
    background-color: #2ECC71; 
    font-size: 28px; 
    color: white; 
    padding: 2;
}
</style>
