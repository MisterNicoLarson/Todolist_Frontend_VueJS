<script setup lang="ts">
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
  h1_title: {
    type: String,
    required: true
  }
});

const emit = defineEmits(['drop', 'startDrag']);

/**
 * Initializes the drag event for a task.
 * @param {DragEvent} event - The drag event.
 * @param {number} taskId - The ID of the task being dragged.
 */
const startDrag = (event: DragEvent, taskId: number) => {
  event.dataTransfer!.dropEffect = 'move';
  event.dataTransfer!.effectAllowed = 'move';
  event.dataTransfer!.setData('itemID', taskId.toString());
  emit('startDrag', event, taskId);
};

/**
 * Handles the dragover event to allow dropping.
 * @param {DragEvent} event - The dragover event.
 */
const onDragOver = (event: DragEvent) => {
  event.preventDefault();
  event.dataTransfer!.dropEffect = 'move';
};

/**
 * Handles the drop event to move a task to a specific column.
 * @param {DragEvent} event - The drop event.
 */
const onDrop = (event: DragEvent) => {
  event.preventDefault();
  const itemID = parseInt(event.dataTransfer!.getData('itemID'));
  emit('drop', itemID);
};
</script>

<template>
  <div class="container" @dragover="onDragOver" @drop="onDrop">
    <div class="title-column">
      <h1>{{ h1_title }}</h1>
    </div>
    <div class="tableau">
      <slot></slot>
    </div>
  </div>
</template>

<style>
:root {
  --full-width: 100%;
  --min-width: 400px;
  --full-height: 100%;
  --min-height: 400px;
  --font-size: 10px;
  --bg-color: aliceblue;
  --text-color: black;
  --fixed-height: 500px;
}

.container {
  display: grid;
  grid-template-rows: auto 1fr;
  margin: 0;
  padding: 0;
  height: var(--fixed-height);
}

.title-column {
  text-align: center;
  background-color: rgba(240, 248, 255, 0.8); /* aliceblue more darker */
  font-size: var(--font-size);
  width: var(--full-width);
  min-width: var(--min-width);
  color: var(--text-color);
  margin: 0;
  padding: 0;
}

.tableau {
  background-color: var(--bg-color);
  width: var(--full-width);
  min-width: var(--min-width);
  margin: 0;
  padding: 0;
  overflow-y: auto;
}
</style>
