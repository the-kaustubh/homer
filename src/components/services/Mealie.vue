<template>
  <Generic :item="item">
    <template #content>
      <p class="title is-4">{{ item.name }}</p>
      <p class="subtitle is-6">
        <template v-if="item.subtitle">
          {{ item.subtitle }}
        </template>
        <template v-else-if="meal"> Today: {{ meal[0].recipe.name }} </template>
        <template v-else-if="stats">
          happily keeping {{ stats.totalRecipes }} recipes organized
        </template>
      </p>
    </template>
  </Generic>
</template>

<script>
import service from "@/mixins/service.js";
import Generic from "./Generic.vue";

export default {
  name: "Mealie",
  components: {
    Generic,
  },
  mixins: [service],
  props: {
    item: Object,
  },
  data: () => ({
    stats: null,
    meal: null,
  }),
  created() {
    this.fetchStatus();
  },
  methods: {
    fetchStatus: async function () {
      const headers = {
        Authorization: "Bearer " + this.item.apikey,
        Accept: "application/json",
      };

      if (this.item.subtitle != null) return;

      this.meal = await this.fetch("/api/groups/mealplans/today", { headers }).catch(
        (e) => console.log(e),
      );
      this.stats = await this.fetch("/api/admin/about/statistics", {
        headers,
      }).catch((e) => console.log(e));
    },
  },
};
</script>
