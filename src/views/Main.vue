<template>
  <div class="mb-3">
    <div class="alert alert-secondary mt-3 mb-2 pb-0">
      <div class="row">
        <div class="col-12 col-md-4 mb-3">
          <div class="form-floating">
            <select
              class="form-select form-select-lg"
              v-model="currentToolLevel"
            >
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
            <select
              class="form-select form-select-lg"
              v-model="targetToolLevel"
            >
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
            <label class="fw-bold text-light">Total Gold Cost</label>
          </div>
        </div>
        <div class="col-12 col-md-4 mb-3">
          <div class="alert alert-success m-0 p-2">
            <p class="m-0 p-0 fw-bold">Total Gains</p>
            <p class="m-0 p-0">
              +{{ resourceGained.toFixed(2) }}% Res, +{{ expGained }} XP
            </p>
          </div>
        </div>
      </div>
    </div>
    <small>Updated to Patch 17</small>
  </div>
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
      pricePer: 200000,
      currentToolLevel: 1,
      targetToolLevel: 2,
      toolLevels: [
        0, // 1
        10,
        30,
        75,
        175, // 5
        350,
        800,
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
        107000, // 25
        125000,
        145000,
        170000
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
      let tier1 = this.getByTier(level, 1, 11);
      let tier2 = this.getByTier(level, 11, 30);

      return { tier1: tier1, tier2: tier2 };
    },
    getResourceByLevel(levelGroup) {
      let resources = 0;

      resources += levelGroup.tier1 * 5; // tier1
      let current = 5;
      for (let x = 0; x < levelGroup.tier2; x += 1) {
        current *= 1.05;
        current = Math.round(current * 100) / 100;
        resources += current;
      }

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
