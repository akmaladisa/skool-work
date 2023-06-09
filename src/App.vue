<template>
  <div class="container">
    <h1 class="text-secondary fw-bold text-center my-4">SchoolWork</h1>

    <div class="d-flex justify-content-center row my-5">
      <div class="col-8">
        <form @submit.prevent="submitTask(taskName, dateRaw)">
          <div class="input-group mb-3">
            <span class="input-group-text" id="inputGroup-sizing-default">Task Name</span>
            <input required v-model="taskName" type="text" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default">
          </div>
          <div class="input-group mb-3">
            <span class="input-group-text" id="inputGroup-sizing-default">Date</span>
            <input required v-model="dateRaw" type="date" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default">
          </div>
          <div class="d-grid gap-2">
            <button class="btn btn-secondary" type="submit">Add</button>
          </div>
        </form>
      </div>
    </div>

    <div class="row d-flex justify-content-center">
      <div class="col-lg-3 col-md-6 col-sm-12">
        <h3 class="text-success text-center">
          Enough Time
        </h3>

        <div v-for="(task, i) in enoughTimeTask" :key="i" class="mt-4">

          <div class="card text-white bg-success mb-3 mx-auto" style="max-width: 18rem;">
            <div class="card-header">{{ task.date }}</div>
            <div class="card-body">
              <h5 class="card-title">{{ task.taskName }}</h5>
              <p class="card-text">due date in <span class="fw-bold">{{ howManyDays(task.date) }}</span> day{{ (howManyDays(task.date)) ? "s " : "" }}</p>
            </div>
            <div class="card-footer">
              <button class="btn-primary btn me-2" @click="addToDoneTask(i, task, enoughTimeTask, finishedTask)">Done</button>
              <button class="btn-danger btn" @click="deleteTask(i, enoughTimeTask)">Delete</button>
            </div>
          </div>

        </div>

      </div>
      <div class="col-lg-3 col-md-6 col-sm-12">
        <h3 class="text-warning text-center">
          Warning Task
        </h3>

        <div v-for="(task, i) in warningTask" :key="i" class="mt-4">

          <div class="card text-white bg-warning mb-3 mx-auto" style="max-width: 18rem;">
            <div class="card-header">{{ task.date }}</div>
            <div class="card-body">
              <h5 class="card-title">{{ task.taskName }}</h5>
              <p class="card-text">due date in <span class="fw-bold">{{ howManyDays(task.date) }}</span> day{{ (howManyDays(task.date)) ? "s " : "" }}</p>
            </div>
            <div class="card-footer">
              <button class="btn-primary btn me-2" @click="addToDoneTask(i, task, warningTask, finishedTask)">Done</button>
              <button class="btn-danger btn" @click="deleteTask(i, warningTask)">Delete</button>
            </div>
          </div>

        </div>
      </div>
      <div class="col-lg-3 col-md-6 col-sm-12">
        <h3 class="text-danger text-center">
          Expired Task
        </h3>

        <div v-for="(task, i) in expiredTask" :key="i" class="mt-4">

          <div class="card text-white bg-danger mb-3 mx-auto" style="max-width: 18rem;">
            <div class="card-header">{{ task.date }}</div>
            <div class="card-body">
              <h5 class="card-title">{{ task.taskName }}</h5>
              <p class="card-text">due date in <span class="fw-bold">{{ howManyDays(task.date) }}</span> day{{ (howManyDays(task.date)) ? "s " : "" }}</p>
            </div>
            <div class="card-footer">
              <button class="btn-primary btn me-2" @click="addToDoneTask(i, task, expiredTask, finishedTask)">Done</button>
              <button class="btn-danger btn" @click="deleteTask(i, expiredTask)">Delete</button>
            </div>
          </div>

        </div>
      </div>
      <div class="col-lg-3 col-md-6 col-sm-12">
        <h3 class="text-primary text-center">
          Finished Task
        </h3>

        <div v-for="(task, i) in finishedTask" :key="i" class="mt-4">

          <div class="card text-white bg-primary mb-3 mx-auto" style="max-width: 18rem;">
            <div class="card-header">{{ task.date }}</div>
            <div class="card-body">
              <h5 class="card-title">{{ task.taskName }}</h5>
              <p class="card-text">completed with <span class="fw-bold">{{ howManyDays(task.date) }}</span> day{{ (howManyDays(task.date)) ? "s " : "" }} remaining</p>
            </div>
            <div class="card-footer">
              <button class="btn-danger btn" @click="deleteTask(i, finishedTask)">Delete</button>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<script setup>

import { computed, reactive, ref } from 'vue';

let enoughTimeTask = reactive([]);
let warningTask = reactive([]);
let expiredTask = reactive([]);
let finishedTask = reactive([]);

let taskName = ref('');
let dateRaw = ref('');

const submitTask = (task, date) => {

  // //get raw inputed date by user
  // const rawInputedDate = date;

  // const inputParts = rawInputedDate.split("/");
  const inputDate = new Date(date);

  //curuentt date
  const currentDate = new Date();

  // Calculate the time difference in milliseconds
  const timeDifference = inputDate.getTime() - currentDate.getTime();

  // Convert the time difference to days
  const daysDifference = Math.ceil(timeDifference / (1000 * 60 * 60 * 24));

  const taskToPushToCertainCategory = {
    taskName: task,
    date: date,
    // howManyDays: daysDifference
  }

  //push to enoughTimeTask
  if( daysDifference > 14 )
    enoughTimeTask.push(taskToPushToCertainCategory)

  //push to warningTask
  if( daysDifference <= 14 && daysDifference > 0 )
    warningTask.push(taskToPushToCertainCategory)

  //push to expiredTask
  if( daysDifference <= 0 )
    expiredTask.push(taskToPushToCertainCategory)

  taskName.value = '';
  dateRaw.value = '';
  
}

const addToDoneTask = (i, task, taskArrayNotDone, taskArrayDone) => {
  if( i > -1 ) {
    taskArrayNotDone.splice(i, 1);
    taskArrayDone.push(task);
  }
}

const deleteTask = (i, taskArray) => {
  if( i > -1 ) {
    taskArray.splice(i, 1);
  }
}

const howManyDays = ( daysGap ) => {
  const dayGap = new Date(daysGap);
  const currentDate = new Date();

  const timeDifference = dayGap.getTime() - currentDate.getTime();

  const daysDifference = Math.ceil( timeDifference / (1000 * 60 * 60 * 24) );

  return daysDifference;
} 

</script>