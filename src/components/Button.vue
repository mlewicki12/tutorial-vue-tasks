<template>
  <button :style='{background: color}' @click="this.$emit('toggle')"
    class='btn'>{{ text }}</button>
</template>

<script>
  export default {
    name: 'Button',
    props: {
      text: String,
      color: String
    },
  }
</script>