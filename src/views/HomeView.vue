<template>
  <WorldMap />
  <AstronautList />
</template>

<script>
import { defineComponent } from 'vue';

// Components
import WorldMap from '@/components/WorldMap.vue';
import AstronautList from '@/components/AstronautList.vue';

export default defineComponent({
  name: 'HomeView',

  components: {
    WorldMap,
    AstronautList,
  },
});
</script>
