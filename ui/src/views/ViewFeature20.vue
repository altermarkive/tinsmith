<template>
  <Feature
    :parametersKeys="parametersKeys"
    :parametersLabels="parametersLabels"
    :resultsKeys="resultsKeys"
    :resultsLabels="resultsLabels"
    :resultsDigits="resultsDigits"
    :coreFunction="coreFunction"
    :blueprintName="blueprintName"
  />
</template>

<script lang="ts">
import Vue from "vue";
import Feature from "@/components/Feature.vue";

interface Core {
  feature20: Function;
}

export default Vue.extend({
  name: "ViewFeature20",

  components: {
    Feature,
  },

  data: () => ({
    parametersKeys: ["D", "G", "R", "V", "T"],
    parametersLabels: { D: "⌀| D", G: "⌀◜ D", R: "⎋ R", V: "⟷ V", T: "⧻ T" },
    resultsKeys: ["S", "L", "Points"],
    resultsLabels: { S: "∡ S", L: "◯ L", Points: "⦙" },
    resultsDigits: { S: 1, L: 0, Points: 0 },
    coreFunction: ((window as unknown) as Core).feature20,
    blueprintName: "feature20",
  }),
});
</script>
