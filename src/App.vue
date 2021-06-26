<template>
  <div id="app" class="container" @click="next()">
    <Counter class="counter" :count="totalVisitedCountries" />
    <Current
      class="current"
      :name="currentCountryName"
      :visitors="currentCountryVisitors.length"
    />
    <Leaderboard
      class="leaderboard"
      :users="users"
      :currentCountry="currentCountryName"
    />
    <MapChart
      class="map"
      :countryData="mapData"
      highColor="#ff0000"
      lowColor="#ffcccc"
      countryStrokeColor="#909090"
      defaultCountryFillColor="#dadada"
    />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import MapChart from "vue-map-chart";
import Counter from "@/components/Counter.vue";
import Current from "@/components/Current.vue";
import Leaderboard from "@/components/Leaderboard.vue";
import userData from "@/assets/users.json";

export enum status {
  OK,
  OUT,
  WINNER,
}

export type user = {
  id: number;
  name: string;
  guess: number;
  visited: string[];
  status: status;
};

@Component({
  components: {
    MapChart,
    Counter,
    Current,
    Leaderboard,
  },
})
export default class App extends Vue {
  private users: user[] = [];
  private visitorsPerCountry: Map<string, string[]> = new Map<
    string,
    string[]
  >();

  private seenCountries: string[] = [];

  private currentCountryName!: string;
  private currentCountryVisitors!: string[];

  created(): void {
    this.users = userData["users"];

    this.users.forEach((user) => {
      user.status = status.OK;

      user.visited.forEach((country) => {
        const visitors = this.visitorsPerCountry.get(country) || [];
        visitors.push(user.name);
        this.visitorsPerCountry.set(country, visitors);
      });
    });

    this.currentCountryName = "?";
    this.currentCountryVisitors = [];
  }

  next(): void {
    if (this.totalVisitedCountries >= this.sortedCountries.length) {
      const usersSortedByDiff: user[] = JSON.parse(JSON.stringify(this.users));
      usersSortedByDiff.sort(
        (a, b) =>
          Math.abs(a.guess - this.totalVisitedCountries) -
          Math.abs(b.guess - this.totalVisitedCountries)
      );
      const winners = usersSortedByDiff.slice(0, 3).map((user) => user.name);
      this.users
        .filter((user) => winners.includes(user.name))
        .map((user) => (user.status = status.WINNER));
      return;
    }
    this.currentCountryName = this.sortedCountries[this.totalVisitedCountries];
    console.log(this.currentCountryName);
    this.currentCountryVisitors =
      this.visitorsPerCountry.get(this.currentCountryName) || [];
    console.log(this.currentCountryVisitors);

    this.seenCountries.push(this.currentCountryName);

    this.users
      .filter((user) => user.guess < this.totalVisitedCountries)
      .map((user) => (user.status = status.OUT));
  }

  get totalVisitedCountries(): number {
    return this.seenCountries.length;
  }

  get sortedCountries(): string[] {
    const countries = [...this.visitorsPerCountry.keys()];
    return countries.sort((a, b) => {
      const visitorsA = this.visitorsPerCountry.get(a)?.length || 0;
      const visitorsB = this.visitorsPerCountry.get(b)?.length || 0;
      if (visitorsA === visitorsB) {
        return a.localeCompare(b);
      } else if (visitorsA > visitorsB) {
        return -1;
      } else {
        return 1;
      }
    });
  }

  get mapData(): any {
    const cd = new Map<string, number>();
    this.visitorsPerCountry.forEach((visitors, country) => {
      if (this.seenCountries.includes(country)) {
        cd.set(country, visitors.length);
      }
    });
    return Object.fromEntries([...cd]);
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px;
}

.container {
  display: grid;
  grid-template-columns: 1fr 3fr;
  grid-template-rows: 0.5fr 0.5fr 3fr;
  grid-gap: 2rem;
}

.counter {
  grid-column-start: 1;
  grid-column-end: 2;
  grid-row-start: 1;
  grid-row-end: 2;
}

.current {
  grid-column-start: 1;
  grid-column-end: 2;
  grid-row-start: 2;
  grid-row-end: 3;
}

.leaderboard {
  grid-column-start: 1;
  grid-column-end: 2;
  grid-row-start: 3;
  grid-row-end: 4;
}

.map {
  grid-column-start: 2;
  grid-column-end: 3;
  grid-row-start: 1;
  grid-row-end: 4;

  border: 1px solid #bbb;
  border-radius: 0.5rem;
  box-shadow: 1px 1px 10px #777;
  padding: 2rem;

  height: auto !important;
}
</style>
