<script setup lang="ts">
import { ref, onMounted } from "vue";
import { repeatWhen, take } from "rxjs/operators";
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
  of,
  timeout,
  interval,
} from "rxjs";
const boxRef = ref<HTMLDivElement | null>(null);
onMounted(() => {
  const source = interval(1000).pipe(take(2));
  const documentClick = fromEvent(document, "click");

  source
    .pipe(repeatWhen(() => documentClick))
    .subscribe((data) => console.log(data));
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
