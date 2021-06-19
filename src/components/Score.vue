<template>
  <div>Score: {{ score }}</div>
</template>

<script>
export default {
  props: {
    score: {
      type: Number,
      default: 0
    }
  }
}
</script>
