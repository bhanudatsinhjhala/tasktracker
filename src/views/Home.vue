<template>
<Addtask v-show="showAddTask" @add-task="addTask" />
  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Addtask from '../components/Addtask.vue'
import Tasks from '../components/Tasks.vue'

export default {
     name: 'Home',
     props:{
         showAddTask: Boolean,
     },
     components:{
         Tasks,
         Addtask
     },
     data() {
         return {
             tasks:[]
         }
     },
     methods:{
    toggleAddTask(){
      this.showAddTask =!this.showAddTask
    },

    async addTask(task){
      const res = await fetch('api/tasks',{
        method: 'POST',
        headers:{
          'Content-type': 'application/json',
        },
         body: JSON.stringify(task),
      })
      const data = await res.json();

      this.tasks=[...this.tasks, data]
    },

    async deleteTask(id){
    //  console.log('task', id);
    if(confirm('Are you sure?')){
      const res = await fetch(`api/tasks/${id}`, {
        method: 'DELETE',
      })

      res.status === 200 ? (this.tasks=this.tasks.filter((task)=> task.id !== id )) 
      : alert("Error deleting task...")
    }
    },
    async toggleReminder(id){
      const taskToToggle = await this.fetchTask(id)

      const updTask = {...taskToToggle, reminder:!taskToToggle.reminder}

      const res = await fetch(`api/tasks/${id}`,{
        method: 'PUT',
        headers:{
          'Content-type': 'application/json'
        },
        body: JSON.stringify(updTask),
      })

      const data = await res.json()
      //console.log(id);
      this.tasks= this.tasks.map((task) => 
        task.id == id ? { ...task, reminder: data.reminder} : task
      )
    },    
    async fetchTasks(){
      const res = await fetch('api/tasks')

      const data = await res.json()

      return data;
    },
    async fetchTask(id){
      const res = await fetch(`api/tasks/${id}`)

      const data = await res.json()

      return data;
    },
  },
  async created(){
    this.tasks = await this.fetchTasks()
  }
}
</script>