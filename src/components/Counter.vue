<template>
  <div class="counter">
    <h3>Besuchte Länder:</h3>
    <h2>{{ count }}</h2>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class Counter extends Vue {
  @Prop() private count!: number;

  increment(): void {
    this.count++;
  }

  decrement(): void {
    this.count--;
  }
}
</script>

<style scoped>
.counter {
  border: 1px solid #bbb;
  border-radius: 0.5rem;
  box-shadow: 1px 1px 10px #777;
  padding: 1rem;
}
</style>
