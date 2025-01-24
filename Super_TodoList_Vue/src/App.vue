<script setup lang="ts">
  import { ref } from 'vue';

  import Task from './components/Task.vue';
  import ColumnTable from './components/ColumnTable.vue';
  import NewTaskButton from './components/NewTaskButton.vue';

  const inProgress = "In Progress";
  const done = "Done !";
  const failed = "Failed";

  const tasksInProgress = ref<{ id: number, taskData: { taskName: string, deadline: string, hour: string }, isEmpty: boolean, isTaskNameEmpty: boolean, isDeadlineEmpty: boolean, isHourEmpty: boolean, isEditable: boolean }[]>([]);
  const tasksDone = ref<{ id: number, taskData: { taskName: string, deadline: string, hour: string }, isEmpty: boolean, isTaskNameEmpty: boolean, isDeadlineEmpty: boolean, isHourEmpty: boolean, isEditable: boolean }[]>([]);
  const tasksFailed = ref<{ id: number, taskData: { taskName: string, deadline: string, hour: string }, isEmpty: boolean, isTaskNameEmpty: boolean, isDeadlineEmpty: boolean, isHourEmpty: boolean, isEditable: boolean }[]>([]);

  let idTask = 0;

  /**
   * Adds a new empty task to the in-progress tasks list if all existing tasks are complete.
   */
  const addTask = () => {
    const allTasksComplete = tasksInProgress.value.every(task =>
      task.taskData.taskName.trim() !== '' &&
      task.taskData.deadline.trim() !== '' &&
      task.taskData.hour.trim() !== ''
    );

    if (allTasksComplete) {
      idTask++;
      tasksInProgress.value.unshift({
        id: idTask,
        taskData: { taskName: '', deadline: '', hour: '' },
        isEmpty: true,
        isTaskNameEmpty: true,
        isDeadlineEmpty: true,
        isHourEmpty: true,
        isEditable: true,
      });
    }
  };

  /**
   * Updates the data of a specific task.
   * @param {number} id - The ID of the task to update.
   * @param {Object} taskData - The new data for the task.
   * @param {string} taskData.taskName - The name of the task.
   * @param {string} taskData.deadline - The deadline of the task.
   * @param {string} taskData.hour - The hour of the task.
   */
  const updateTaskData = (id: number, taskData: { taskName: string, deadline: string, hour: string }) => {
    const task = findTaskById(id);
    if (task) {
      task.taskData = taskData;
      task.isTaskNameEmpty = task.taskData.taskName.trim() === '';
      task.isDeadlineEmpty = task.taskData.deadline.trim() === '';
      task.isHourEmpty = task.taskData.hour.trim() === '';
      task.isEmpty = task.isTaskNameEmpty && task.isDeadlineEmpty && task.isHourEmpty;

      if (isTaskFailed(task.taskData.deadline, task.taskData.hour)) {
        moveTaskToFailed(task);
      }
    }
  };

  /**
   * Checks if a task has failed by comparing the current date and time with the task's deadline and hour.
   * @param {string} deadline - The deadline of the task.
   * @param {string} hour - The hour of the task.
   * @returns {boolean} - Returns true if the task has failed, otherwise false.
   */
  const isTaskFailed = (deadline: string, hour: string): boolean => {
    const now = new Date();
    const taskDeadline = new Date(`${deadline}T${hour}`);
    return now > taskDeadline;
  };

  /**
   * Moves a task to the "Failed" column.
   * @param {Object} task - The task to move.
   * @param {number} task.id - The ID of the task.
   * @param {Object} task.taskData - The data of the task.
   * @param {string} task.taskData.taskName - The name of the task.
   * @param {string} task.taskData.deadline - The deadline of the task.
   * @param {string} task.taskData.hour - The hour of the task.
   * @param {boolean} task.isEmpty - Indicates if the task is empty.
   * @param {boolean} task.isTaskNameEmpty - Indicates if the task name is empty.
   * @param {boolean} task.isDeadlineEmpty - Indicates if the deadline is empty.
   * @param {boolean} task.isHourEmpty - Indicates if the hour is empty.
   */
  const moveTaskToFailed = (task: { id: number, taskData: { taskName: string, deadline: string, hour: string }, isEmpty: boolean, isTaskNameEmpty: boolean, isDeadlineEmpty: boolean, isHourEmpty: boolean, isEditable: boolean }) => {
    task.isEditable = false;
    removeTaskById(task.id);
    tasksFailed.value.unshift(task);
  };

  /**
   * Finds a task by its ID in all columns.
   * @param {number} id - The ID of the task to find.
   * @returns {Object|undefined} - Returns the found task or undefined if no task is found.
   */
  const findTaskById = (id: number) => {
    return tasksInProgress.value.find(t => t.id === id) ||
           tasksDone.value.find(t => t.id === id) ||
           tasksFailed.value.find(t => t.id === id);
  };

  /**
   * Removes a task by its ID from all columns.
   * @param {number} id - The ID of the task to remove.
   */
  const removeTaskById = (id: number) => {
    tasksInProgress.value = tasksInProgress.value.filter(t => t.id !== id);
    tasksDone.value = tasksDone.value.filter(t => t.id !== id);
    tasksFailed.value = tasksFailed.value.filter(t => t.id !== id);
  };

  /**
   * Moves a task to the "Done" column.
   * @param {Object} task - The task to move.
   * @param {number} task.id - The ID of the task.
   * @param {Object} task.taskData - The data of the task.
   * @param {string} task.taskData.taskName - The name of the task.
   * @param {string} task.taskData.deadline - The deadline of the task.
   * @param {string} task.taskData.hour - The hour of the task.
   * @param {boolean} task.isEmpty - Indicates if the task is empty.
   * @param {boolean} task.isTaskNameEmpty - Indicates if the task name is empty.
   * @param {boolean} task.isDeadlineEmpty - Indicates if the deadline is empty.
   * @param {boolean} task.isHourEmpty - Indicates if the hour is empty.
   */
  const moveTaskToDone = (task: { id: number, taskData: { taskName: string, deadline: string, hour: string }, isEmpty: boolean, isTaskNameEmpty: boolean, isDeadlineEmpty: boolean, isHourEmpty: boolean, isEditable: boolean }) => {
    removeTaskById(task.id);
    tasksDone.value.unshift(task);
  };

  /**
   * Moves a task to the "In Progress" column.
   * @param {Object} task - The task to move.
   * @param {number} task.id - The ID of the task.
   * @param {Object} task.taskData - The data of the task.
   * @param {string} task.taskData.taskName - The name of the task.
   * @param {string} task.taskData.deadline - The deadline of the task.
   * @param {string} task.taskData.hour - The hour of the task.
   * @param {boolean} task.isEmpty - Indicates if the task is empty.
   * @param {boolean} task.isTaskNameEmpty - Indicates if the task name is empty.
   * @param {boolean} task.isDeadlineEmpty - Indicates if the deadline is empty.
   * @param {boolean} task.isHourEmpty - Indicates if the hour is empty.
   */
  const moveTaskToInProgress = (task: { id: number, taskData: { taskName: string, deadline: string, hour: string }, isEmpty: boolean, isTaskNameEmpty: boolean, isDeadlineEmpty: boolean, isHourEmpty: boolean, isEditable: boolean }) => {
    removeTaskById(task.id);
    tasksInProgress.value.unshift(task);
  };
</script>

<template>
  <NewTaskButton @click="addTask"></NewTaskButton>
  <div class="row">
    <div class="block">
      <ColumnTable :h1_title="inProgress">
        <Task
          v-for="task in tasksInProgress"
          :key="task.id"
          :taskData="task.taskData"
          :isEditable="task.isEditable"
          @update:taskData="updateTaskData(task.id, $event)"
          @moveToDone="moveTaskToDone(task)"
          @moveToFailed="moveTaskToFailed(task)"
          :class="{
            'task-name-empty': task.isTaskNameEmpty,
            'deadline-empty': task.isDeadlineEmpty,
            'hour-empty': task.isHourEmpty
          }"
        ></Task>
      </ColumnTable>
    </div>
    <div class="block">
      <ColumnTable :h1_title="done">
        <Task
          v-for="task in tasksDone"
          :key="task.id"
          :taskData="task.taskData"
          :isEditable="task.isEditable"
          @update:taskData="updateTaskData(task.id, $event)"
          @moveToInProgress="moveTaskToInProgress(task)"
          @moveToFailed="moveTaskToFailed(task)"
          :class="{
            'task-name-empty': task.isTaskNameEmpty,
            'deadline-empty': task.isDeadlineEmpty,
            'hour-empty': task.isHourEmpty
          }"
        ></Task>
      </ColumnTable>
    </div>
    <div class="block">
      <ColumnTable :h1_title="failed">
        <Task
          v-for="task in tasksFailed"
          :key="task.id"
          :taskData="task.taskData"
          :isEditable="task.isEditable"
          @update:taskData="updateTaskData(task.id, $event)"
          @moveToInProgress="moveTaskToInProgress(task)"
          @moveToDone="moveTaskToDone(task)"
          :class="{
            'task-name-empty': task.isTaskNameEmpty,
            'deadline-empty': task.isDeadlineEmpty,
            'hour-empty': task.isHourEmpty
          }"
        ></Task>
      </ColumnTable>
    </div>
  </div>
</template>

<style>
.row {
  display: flex;
  justify-content: space-between;
  width: 100%;
  gap: 2px;
  text-align: center;
}

.block {
  flex: 1;
  min-width: 100px;
}

.task-name-empty input {
  border-color: red;
}

.deadline-empty input {
  border-color: red;
}

.hour-empty input {
  border-color: red;
}
</style>
