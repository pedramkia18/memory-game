<template>
  <span>{{ secounds }}</span>
</template>

<script>
export default {
  name: 'Counter',
  data() {
    return {
      secounds: 10,
      interval: null,
    };
  },
  mounted() {
    this.interval = setInterval(() => {
      this.secounds -= 1;
      if (this.secounds === 0) {
        clearInterval(this.interval);
        this.$emit('finished');
      }
    }, 1000);
  },
  destroyed() {
    clearInterval(this.interval);
  },
};
</script>
