<template>
  <div id="right">
    <h1>Développement CRM</h1>
    <div class="horizontal">
      <img src="/images/horizontal.png" />
    </div>

    <p>
      Lorem Ipsum is simply dummy text of the printing and typesetting industry.
      Lorem Ipsum has been the industry's standard dummy text ever since the
      1500s
    </p>

    <div class="users-icon"><img src="/images/users.png" /></div>

    <div class="tasks">
      <div class="add-tasks">
        <h2>Today's Task</h2>
        <div class="add-action"><img src="/images/add.png" /></div>
      </div>

      <ul class="tasks-list">
        <li v-for="task in todaytasks" v-bind:key="task.id">
          <div class="info">
            <div class="left">
              <label class="myCheckbox">
                <input type="checkbox" name="test"
                :checked="task.completed"
                @change="updateTodayTask(task.taskId)"/>
                <span></span>
              </label>
              <h4>{{task.title}}</h4>
            </div>
            <div class="right">
              <img src="/images/edit.png" alt="">
              <img src="/images/del.png" alt=""
              @click="deleteTask(task.taskId)">

              <button v-bind:class="{inprogress: !task.approved, approved: task.approved,}">
                {{task.approved ? "Approved" : "In progress"}}
              </button>
            </div>
          </div>
        </li>
      </ul>
    </div>

    <div class="upcoming">
      <div class="add-tasks">
        <h2>Upcoming</h2>
        <div class="add-action"><img src="/images/add.png" /></div>
      </div>

      <form action="" @submit="addUpcomingTask">
        <input type="text" v-model="newTask"/>
      </form>

      <ul class="tasks-list">
        <li v-for="upcomingtask in upcoming" v-bind:key="upcomingtask.id">
          <div class="info">
            <div class="left">
              <label class="myCheckbox">
                <input type="checkbox" name="test"
                :checked="upcomingtask.completed"
                @change="checkUpcoming(upcomingtask.taskId)"/>
                <span></span>
              </label>
              <h4>{{upcomingtask.title}}</h4>
            </div>
            <div class="right">
              <img src="/images/edit.png" alt="">
              <img src="/images/del.png" alt=""
              @click="delUpcoming(upcomingtask.taskId)">

              <button v-bind:class="{inprogress: !upcoming.waiting}">
                Waiting
              </button>
            </div>
          </div>
        </li>
      </ul>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return { 
      todaytasks:[],
      upcoming:[],
      newtask:"",
    };
  },
  created() { 
    this.fetchTodayTasks();
    this.fetchUpcoming();
  },

  methods: { 
    /*Upcoming Task */

    /* Get upcoming task */
    fetchUpcoming(){
      fetch("api/upcoming")
      .then((res) => res.json())
      .then(({data}) => {
        this.upcoming = data;
        console.log(data);
      })
      .catch((err) => console.log(err));
    },
    

    //Add upcoming task
    addUpcomingTask(e){
      e.preventDefault();
      if (this.upcoming.length > 4) {
        alert("Veuillez achever les tâches actuelles");
      } else {
        const newTask = {
          title:this.newTask,
          waiting:true,
          taskId: Math.random().toString(36).substring(7),

        };

        //Post request
        fetch("api/upcoming",{
          method:"POST",
          headers:{
            "content-type":"application/json",
          },
          body: JSON.stringify(newTask),
        }).then(()=> this.upcoming.push(newTask));

        //Clear or delete new Task
        this.newTask = "";
        
      }
    },

    

    delUpcoming(taskId){
      if(confirm("Etes-vous certain")){
        
      
        fetch(`/api/upcoming/${taskId}`,{
          method:"delete",
        })
        .then((res) => res.json())
        .then(() => {
          this.upcoming = this.upcoming.filter(
            ({taskId:id}) => id !== taskId
          );
        })
        .catch((err) => console.log(err));
      }
    },

    //Check upcoming task
    checkUpcoming(taskId){
      if(this.todaytasks.length > 4){
        alert("Veuillez achever les tâches actuelles");
        window.location.href="/";
      }
      else{
        this.addDailyTask(taskId);

        //Deleted from this task from db
        fetch(`/api/upcoming/${taskId}`,{method:"delete"}).then(
          () =>
        (this.upcoming = this.upcoming.filter(
          ({taskId:id})=> id !== taskId
        ))
        );
      }

    },

    /*Today Task method*/
    //Get Today Task
    
    fetchTodayTasks(){
      fetch("/api/dailytask").then(
          (res) => res.json()).then(({data})=> this.todaytasks = data).catch(err => console.log(err));
      },

    //Add daily task
    addDailyTask(taskId){
      //get task

      const task = this.upcoming.filter(({taskId:id})=> id == taskId)[0];

      //Post request
      fetch("/api/upcoming",{
          method:"POST",
          headers:{
            "content-type":"application/json",
          },
          body: JSON.stringify(task),
        }).then(()=> this.todaytasks.unshift(task))
        .catch((err) => console.log(err));

    },

    deleteTask(taskId){
      if(confirm("Etes-vous certain")){
        
      
        fetch(`/api/dailytask/${taskId}`,{
          method:"delete",
        })
        .then((res) => res.json())
        .then(() => {
          this.todaytasks = this.todaytasks.filter(
            ({taskId:id}) => id !== taskId
          );
        })
        .catch((err) => console.log(err));
      }
    },

    updateTodayTask(taskId){
      if(confirm("Tâche complétée ?")){
        
      
        fetch(`/api/dailytask/${taskId}`,{
          method:"delete",
        })
        .then((res) => res.json())
        .then(() => {
          this.todaytasks = this.todaytasks.filter(
            ({taskId:id}) => id !== taskId
          );
        })
        .catch((err) => console.log(err));
      }
    },
  },

  

    
};
</script>

<style>
</style>