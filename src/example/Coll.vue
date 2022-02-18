<script setup lang="ts">
import { ref, onMounted } from "vue";
import { useLocalStorage } from "@vueuse/core";
import {
  fromEvent,
  map,
  concatAll,
  takeUntil,
  concatMap,
  takeWhile,
  filter,
} from "rxjs";
const boxRef = ref<HTMLDivElement | null>(null);
const gapRef = ref<HTMLDivElement | null>(null);
const leftRef = useLocalStorage("LEFT_OFFSET", 200);
onMounted(() => {
  fromEvent(gapRef.value, "mousedown")
    .pipe(
      concatMap((xx) =>
        fromEvent(boxRef.value, "mousemove").pipe(
          takeUntil(fromEvent(boxRef.value, "mouseup")),
          map((x) => x.clientX - xx.layerX),
          filter((x) => x > 100 || x < 500)
        )
      )
    )
    .subscribe((x) => {
      leftRef.value = x;
    });
});
</script>

<template>
  <div class="box" ref="boxRef">
    <div class="left" :style="{ width: `${leftRef}px` }">123</div>
    <div class="right">123</div>
    <div class="gap" ref="gapRef" :style="{ left: `${leftRef}px` }"></div>
  </div>
</template>

<style scoped lang="scss">
.box {
  position: relative;
  display: flex;
  width: 100vw;
  height: 100vh;
  background: #eee;
  .left {
    background-color: red;
  }
  .right {
    flex: 1;
    background-color: blue;
  }
  .gap {
    height: 100%;
    width: 5px;
    left: 100px;
    position: absolute;
    background-color: lightblue;
    cursor: col-resize;
  }
}
</style>
