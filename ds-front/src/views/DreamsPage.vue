<script lang="ts" setup>
import NewDream from "@/components/dreams/NewDream.vue";
import DreamList from "@/components/dreams/DreamList.vue";
import { useMainStore } from "@/stores/main";
import { useDreamStore } from "@/stores/dreams";
import { useDisplay } from "vuetify";
import { onMounted, ref } from "vue";
import { storeToRefs } from "pinia";

const mainStore = useMainStore();
const dreamStore = useDreamStore();
const { settings } = storeToRefs(mainStore);
const mobile = useDisplay().xs.value;
const show = ref(false);
const tab = ref(0);

function handleTabs(index: number): void {
  tab.value = index;
  dreamStore.currentTab = index;
}

onMounted(() => {
  setTimeout(() => (show.value = true), 100);
  tab.value = dreamStore.currentTab;
});
</script>

<template>
  <v-fade-transition hide-on-leave>
    <div v-if="show">
      <div v-if="mobile">
        <v-tabs
          :model-value="tab"
          @update:modelValue="handleTabs"
          :color="settings.colors.textColor"
          :bg-color="settings.colors.backgroundColor"
          :grow="mobile"
          fixed-tabs
        >
          <v-tab :value="0">new</v-tab>
          <v-tab :value="1">all</v-tab>
        </v-tabs>

        <v-window v-model="tab">
          <v-window-item :value="0">
            <NewDream />
          </v-window-item>
          <v-window-item :value="1">
            <DreamList />
          </v-window-item>
        </v-window>
      </div>
      <v-container v-else>
        <v-row>
          <v-col cols="6">
            <NewDream />
          </v-col>
          <v-col cols="6">
            <DreamList />
          </v-col>
        </v-row>
      </v-container>
    </div>
  </v-fade-transition>
</template>
