<template>
  <WorldMap />
</template>

<script>
import { defineComponent } from 'vue';

// Components
import WorldMap from '@/components/WorldMap.vue';

export default defineComponent({
  name: 'HomeView',

  components: {
    WorldMap,
  },
});
</script>
