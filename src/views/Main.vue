<template>
  <div class="alert alert-secondary mt-3 mb-3">
    <div class="row">
      <div class="col-12 col-md-4 mb-3">
        <div class="form-floating">
          <select class="form-select form-select-lg" v-model="currentToolLevel">
            <option
              v-for="(level, i) in toolLevels"
              :key="i + 1"
              v-bind:value="i + 1"
            >
              {{ i + 1 }}
            </option>
          </select>
          <label class="text-light fw-bold">Current tool level</label>
        </div>
      </div>
      <div class="col-12 col-md-4 mb-3">
        <div class="form-floating">
          <select class="form-select form-select-lg" v-model="targetToolLevel">
            <option
              v-for="(level, i) in toolLevels"
              :key="i + 1"
              v-bind:value="i + 1"
            >
              {{ i + 1 }}
            </option>
          </select>
          <label class="text-light fw-bold">Target tool level</label>
        </div>
      </div>
      <div class="col-12 col-md-4 mb-3">
        <div class="form-floating">
          <input
            type="number"
            min="0"
            step="1"
            class="form-control form-control-lg"
            v-model="pricePer"
          />
          <label class="text-light fw-bold">Gold per components</label>
        </div>
      </div>
      <div class="col-12 col-md-4 mb-3">
        <span>+{{ resourceGained }}% resource</span><br />
        <span>+{{ expGained }} experience</span>
      </div>
      <div class="col-12 col-md-4 mb-3">
        <div class="form-floating">
          <input
            disabled
            class="form-control form-control-lg"
            :value="componentsNeeded.toLocaleString()"
          />
          <label class="fw-bold text-light">Components needed</label>
        </div>
      </div>

      <div class="col-12 col-md-4 mb-3">
        <div class="form-floating">
          <input
            disabled
            class="form-control form-control-lg"
            :value="totalCost.toLocaleString()"
          />
          <label class="fw-bold text-light">Total Gold</label>
        </div>
      </div>
    </div>
  </div>
  <small>Updated to Patch 13</small>
</template>

<script>
// @ is an alias to /src
//import comp from "@/components/comp.vue";

export default {
  name: "Main",
  components: {
    //    comp,
  },
  data() {
    return {
      pricePer: 75000,
      currentToolLevel: 1,
      targetToolLevel: 16,
      toolLevels: [
        0, // 1
        5,
        25,
        60,
        150, // 5
        350,
        750,
        1500,
        2500,
        4000, // 10
        6000,
        8500,
        11500,
        15000,
        19000, // 15
        23500,
        28500,
        34000,
        40000,
        47000, // 20
        55000,
        65000,
        77000,
        91000,
        107000 // 25
      ]
    };
  },
  computed: {
    levelDifference() {
      return this.targetToolLevel - this.currentToolLevel;
    },
    expGained() {
      return this.levelDifference * 2;
    },
    resourceGained() {
      let totalResources = 0;

      let currentLevels = this.getLevelsByTier(this.currentToolLevel);
      let currentResources = this.getResourceByLevel(currentLevels);

      let targetLevels = this.getLevelsByTier(this.targetToolLevel);
      let targetResources = this.getResourceByLevel(targetLevels);

      totalResources = targetResources - currentResources;

      return totalResources;
    },
    totalCost() {
      return this.componentsNeeded * this.pricePer;
    },
    componentsNeeded() {
      let currentComponents = this.getTotalComponents(
        this.currentToolLevel - 1
      );

      let targetComponents = this.getTotalComponents(this.targetToolLevel - 1);

      let needed = targetComponents - currentComponents;
      return needed;
    }
  },
  methods: {
    getTotalComponents(level) {
      let total = this.toolLevels[level];

      if (level > 0) {
        total += this.getTotalComponents(level - 1);
      }

      return total;
    },
    getLevelsByTier(level) {
      let tier1 = this.getByTier(level, 0, 14);
      let tier2 = this.getByTier(level, 14, 19);
      let tier3 = this.getByTier(level, 19, 24);
      let tier4 = this.getByTier(level, 24, 29);

      return { tier1: tier1, tier2: tier2, tier3: tier3, tier4: tier4 };
    },
    getResourceByLevel(levelGroup) {
      let resources = 0;

      resources += levelGroup.tier1 * 5; // tier1
      resources += levelGroup.tier2 * 6; // tier2
      resources += levelGroup.tier3 * 7; // tier3
      resources += levelGroup.tier4 * 8; // tier4

      return resources;
    },

    getByTier(level, min, max) {
      if (level < min) {
        return 0;
      }

      if (level > max) {
        level = max;
      }

      return level - min;
    }
  }
};
</script>

<style scoped></style>
