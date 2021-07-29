<template>
  <button @click="onclick()" :style="{ background: color }" class="btn">
    {{ title }}
  </button>
</template>

<script>
export default {
  name: "Button",
  props: {
    title: String,
    color: String,
  },
  methods: {
    onclick() {
      this.$emit("btn-click")
    },
  },
};
</script>