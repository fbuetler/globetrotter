<template>
  <div class="leaderboard">
    <h3>Geschätzte Anzahl Länder:</h3>
    <LeaderboardItem
      v-for="user in sortedUsers"
      :key="user.id"
      :user="user"
      :currentCountry="currentCountry"
    />
  </div>
</template>

<script lang="ts">
import { user } from "@/App.vue";
import { Component, Prop, Vue } from "vue-property-decorator";
import LeaderboardItem from "@/components/LeaderboardItem.vue";

@Component({
  components: {
    LeaderboardItem,
  },
})
export default class Leaderboard extends Vue {
  @Prop() private users!: user[];
  @Prop() private currentCountry!: string;

  get sortedUsers(): user[] {
    return this.users.sort((a, b) =>
      a.guess === b.guess
        ? a.name.localeCompare(b.name)
        : a.guess > b.guess
        ? -1
        : 1
    );
  }
}
</script>

<style scoped>
.leaderboard {
  border: 1px solid #bbb;
  border-radius: 0.5rem;
  box-shadow: 1px 1px 10px #777;
  list-style: none;
  padding: 1rem;
}
</style>
