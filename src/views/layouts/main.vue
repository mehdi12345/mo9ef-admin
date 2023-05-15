<script>
import Horizontal from "./horizontal";

export default {
  components: { Horizontal },
  data() {
    return {};
  },

  methods: {},
};
</script>

<template>
  <div>
    <Horizontal>
      <slot />
    </Horizontal>
  </div>
</template>
