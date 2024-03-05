<template>
  <div>
    <div class="bg-red-500 text-white p-4 my-4">
      {{ msg }}
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';

export default defineComponent({
  name: "AlertComponent",
  props: {
    msg: {
      type: String as PropType<string>,
      default: "No articles available.",
    },
  },
});
</script>
