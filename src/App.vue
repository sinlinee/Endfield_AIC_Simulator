<script setup>
import { ref } from "vue";

const offset = ref({ x: 0, y: 0 });
const isDragging = ref(false);

let pointerId = null;
let startPointer = { x: 0, y: 0 };
let startOffset = { x: 0, y: 0 };

function handlePointerDown(event) {
  pointerId = event.pointerId;
  isDragging.value = true;
  startPointer = { x: event.clientX, y: event.clientY };
  startOffset = { ...offset.value };
  event.currentTarget.setPointerCapture(pointerId);
}

function handlePointerMove(event) {
  if (!isDragging.value || event.pointerId !== pointerId) {
    return;
  }

  offset.value = {
    x: startOffset.x + event.clientX - startPointer.x,
    y: startOffset.y + event.clientY - startPointer.y,
  };
}

function handlePointerUp(event) {
  if (event.pointerId !== pointerId) {
    return;
  }

  isDragging.value = false;
  event.currentTarget.releasePointerCapture(pointerId);
  pointerId = null;
}
</script>

<template>
  <main class="whiteboard-shell">
    <header class="whiteboard-titlebar">
      <p class="whiteboard-titlebar__eyebrow">Infinite Grid Whiteboard</p>
      <h1>Endfield AIC Simulator</h1>
    </header>

    <section
      class="whiteboard"
      :class="{ 'whiteboard--dragging': isDragging }"
      @pointerdown="handlePointerDown"
      @pointermove="handlePointerMove"
      @pointerup="handlePointerUp"
      @pointercancel="handlePointerUp"
    >
      <div
        class="whiteboard__grid"
        :style="{
          '--offset-x': `${offset.x}px`,
          '--offset-y': `${offset.y}px`,
        }"
      >
        <div class="whiteboard__origin">
          <span>0, 0</span>
        </div>
      </div>

      <div class="whiteboard__hint">
        <span>Drag to pan</span>
      </div>
    </section>
  </main>
</template>
