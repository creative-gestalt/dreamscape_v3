<script lang="ts" setup>
import { computed, onMounted, ref } from "vue";
import { useMainStore } from "@/stores/main";
import { useDreamStore } from "@/stores/dreams";
import { storeToRefs } from "pinia";
import { DreamDate } from "@/interfaces/dream.interface";
import { useRouter } from "vue-router";

// router
const router = useRouter();
// stores
const mainStore = useMainStore();
const dreamStore = useDreamStore();
const { settings } = storeToRefs(mainStore);
const { dates, years } = storeToRefs(dreamStore);
const { mstDate } = mainStore;
const { getDreamDates } = dreamStore;
// data
const selectedYear = ref("");

// computed
const localDreamDates = computed(() => {
  const ldd: any[] = [];
  dates.value.map((dreamDate: DreamDate) => {
    ldd.push({
      key: dreamDate._id,
      highlight: {
        color: "purple",
        fillMode: "solid",
        contentClass: "italic",
      },
      dates: new Date(dreamDate.date),
      customData: dreamDate,
    });
  });
  return ldd;
});

function openDreamView(day: any): void {
  if (day.attributes[0]) {
    router.push(`/dream/${day.attributes[0].key}/view`);
  }
}
function getNewDreamDates(): void {
  getDreamDates(selectedYear.value);
}

onMounted(() => {
  selectedYear.value = mstDate().slice(0, 4);
});
</script>

<template>
  <v-container>
    <v-row>
      <v-col>
        <v-sheet
          class="ma-auto mb-3"
          :color="settings.colors.topBarColor"
          rounded
        >
          <v-slide-group :model-value="selectedYear" show-arrows>
            <v-slide-group-item
              #="{ isSelected }"
              v-for="year in years"
              :value="year"
              :key="year"
            >
              <v-btn
                class="ma-2"
                @click="
                  selectedYear = year;
                  getNewDreamDates();
                "
                :color="
                  isSelected
                    ? settings.colors.completeBtnColor
                    : settings.colors.topBarColor
                "
                >{{ year }}
              </v-btn>
            </v-slide-group-item>
          </v-slide-group>
        </v-sheet>
      </v-col>
    </v-row>
    <v-calendar
      @dayclick="openDreamView"
      :from-page="{ month: new Date().getMonth() + 1, year: selectedYear }"
      :columns="$screens({ lg: 4 }, 1)"
      :rows="$screens({ lg: 3 }, 2)"
      :attributes="localDreamDates"
      :style="{
        borderColor: settings.colors.iconColor,
        backgroundColor: settings.colors.topBarColor,
      }"
      is-expanded
      is-dark
    >
    </v-calendar>
  </v-container>
</template>

<style lang="scss">
.v-calendar-daily_head-day-label {
  display: none;
}
</style>
