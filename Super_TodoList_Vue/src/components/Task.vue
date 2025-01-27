<script setup lang="ts">
import { ref, watch } from 'vue';

const props = defineProps<{
  taskData: { taskName: string, deadline: string, hour: string },
  isEditable: boolean,
  backgroundColor: string
}>();

const taskName = ref(props.taskData.taskName);
const deadline = ref(props.taskData.deadline);
const hour = ref(props.taskData.hour);

const emit = defineEmits(['update:taskData']);

watch([taskName, deadline, hour], ([newTaskName, newDeadline, newHour]) => {
  emit('update:taskData', { taskName: newTaskName, deadline: newDeadline, hour: newHour });
});
</script>

<template>
  <div class='post-it' :style="{ backgroundColor: props.backgroundColor }">
    <div class="row">
      <p class="column">Task name</p>
      <input class="column" maxlength="50" v-model="taskName" :disabled="!props.isEditable" @input="emit('update:taskData', { taskName, deadline, hour })"></input>
    </div>

    <div class="row">
      <p class="column">Deadline</p>
      <input class="column" type="date" v-model="deadline" :disabled="!props.isEditable" @input="emit('update:taskData', { taskName, deadline, hour })"></input>
    </div>

    <div class="row">
      <p class="column">Hour</p>
      <input class="column" type="time" v-model="hour" :disabled="!props.isEditable" @input="emit('update:taskData', { taskName, deadline, hour })"></input>
    </div>

    <!-- Remplacement de ::before -->
    <div class="corner"></div>

    <!-- Remplacement de ::after -->
    <div class="curve"></div>
  </div>
</template>

<style>
/* post-it :  https://codepen.io/jweden/pen/kGBBpM */
.post-it {
  position: relative;
  overflow: hidden;
  margin: 5px auto;
  padding: 50px;
  border-radius: 0 0 0 30px/45px;
  box-shadow:
    inset 0 -40px 40px rgba(0, 0, 0, 0.2),
    inset 0 25px 10px rgba(0, 0, 0, 0.2),
    0 5px 6px 5px rgba(0, 0, 0, 0.2);
  font-family: 'Permanent Marker', cursive;
  line-height: 1.7em;
  font-size: 19px;
  display: inline-block;
  word-wrap: break-word;
  max-width: 100%;
  box-sizing: border-box;
}

/* Remplacement de ::before */
.corner {
  position: absolute;
  width: 20px;
  height: 25px;
  background: inherit;
  box-shadow:
    3px -2px 10px rgba(0, 0, 0, 0.2),
    inset 15px -15px 15px rgba(0, 0, 0, 0.3);
  left: 0;
  bottom: 0;
  z-index: 2;
  transform: skewX(25deg);
}

/* Remplacement de ::after */
.curve {
  position: absolute;
  width: 75%;
  height: 20px;
  border-radius: 50%;
  bottom: 0px;
  left: 10%;
}

.row {
  display: flex;
  align-items: center;
}

.column {
  flex: 1;
  text-align: left;
  margin: 0 10px;
}

input {
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 5px;
  min-width: 100px;
}

p {
  color: black;
}
</style>
