<script setup lang="ts">
import { ref, onMounted } from "vue";
import { useLocalStorage } from "@vueuse/core";
import { fromEvent, map, concatAll, takeUntil, concatMap } from "rxjs";
const boxRef = ref(null);
const dragRef = ref(null);
const leftRef = useLocalStorage("LEFT", 0);
const topRef = useLocalStorage("TOP", 0);
onMounted(() => {
  fromEvent(dragRef.value, "mousedown")
    .pipe(
      concatMap((dragEvent) =>
        fromEvent(boxRef.value, "mousemove").pipe(
          takeUntil(fromEvent(dragRef.value, "mouseup")),
          map((boxEvent) => ({
            left: boxEvent.pageX - dragEvent.layerX,
            top: boxEvent.pageY - dragEvent.layerY,
          }))
        )
      )
    )
    .subscribe(({ left, top }) => {
      leftRef.value = left;
      topRef.value = top;
    });
});
</script>

<template>
  <div class="box" ref="boxRef">
    <div
      class="drag"
      ref="dragRef"
      :style="{ left: `${leftRef}px`, top: `${topRef}px` }"
    >
      drag
    </div>
  </div>
</template>

<style scoped lang="scss">
.box {
  position: relative;
  width: 100vw;
  height: 100vh;
  background: #eee;
  .drag {
    position: absolute;
    width: 50px;
    height: 50px;
    left: 0;
    top: 0;
    background: pink;
    box-shadow: 2px 2px 4px;
  }
}
</style>
