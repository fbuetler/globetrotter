<template>
  <div
    class="item"
    :class="{ highlighted: isHighlighted, out: isOut, winner: isWinner }"
  >
    <span class="user">{{ user.name }}</span>
    <span class="guess"> {{ user.guess }} </span>
  </div>
</template>

<script lang="ts">
import { user } from "@/App.vue";
import { status } from "@/App.vue";
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class LeaderboardItem extends Vue {
  @Prop() private user!: user;
  @Prop() private currentCountry!: string;

  get isHighlighted(): boolean {
    return this.user.visited.includes(this.currentCountry);
  }

  get isOut(): boolean {
    return this.user.status === status.OUT;
  }

  get isWinner(): boolean {
    return this.user.status === status.WINNER;
  }
}
</script>

<style scoped>
.item {
  align-items: center;
  display: flex;
  justify-content: space-between;
  padding: 0.25rem;
}
.highlighted {
  background-color: rgb(251, 238, 56);
}
.out {
  text-decoration: line-through;
  color: #909090;
}
.winner {
  background-color: lightgreen;
}
.user {
  text-align: center;
  width: 40%;
}
.guess {
  text-align: center;
  width: 40%;
}
</style>
