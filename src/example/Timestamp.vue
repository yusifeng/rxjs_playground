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
  timestamp,
  scan,
} from "rxjs";
const boxRef = ref<HTMLDivElement | null>(null);
onMounted(() => {
  fromEvent(boxRef.value, "mousedown")
    .pipe(
      timestamp(),
      map((x) => x.timestamp),
      scan((acc, v) => {
        return {
          current: v,
          gap: acc.current ? v - acc.current : v - acc,
        };
      }),
      filter((x) => x.gap),
      map((x) => x.gap)
    )
    .subscribe((x) => {
      console.log(x.token)
);
    });
});
</script>

<template>
  <div class="box" ref="boxRef">
    <div class="cube"></div>
  </div>
</template>

<style scoped lang="scss">
.box {
  position: relative;
  display: flex;
  width: 100vw;
  height: 100vh;
  background: #eee;
  .cube {
    width: 100px;
    height: 100px;
    background-color: red;
  }
}
</style>
