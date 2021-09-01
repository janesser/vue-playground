<template>
  <section>
    <h1 class="header">Nuxxt TypeScript Starter</h1>
    <div class="cards">
      <Card
        v-for="person in people"
        :key="person.id"
        :person="person"
      ></Card>
      <Valid></Valid>
    </div>
  </section>
</template>

<script lang="ts">
import {
  Component,
  Vue
} from "nuxt-property-decorator"
import { State } from "vuex-class"
import { Person } from "~/types";
import Card from "~/components/Card.vue"
import Valid from "~/components/Valid.vue"

@Component({
  components: {
    Card
  }
})
export default class extends Vue {
  @State people: Person
}
</script>

<style scoped>
.header {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

.cards {
  display: flex;
  flex-wrap: wrap;
}
</style>
