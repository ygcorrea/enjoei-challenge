<template>
  <div :class="[
    'c-container',
    { '-with-gutter': gutter }
  ]">
    <slot />
  </div>
</template>

<script>
  export default {
    props: {
      gutter: {
        type: Boolean,
        default: true
      }
    }
  }
</script>

<style scoped lang="scss">
  .c-container {
    max-width: 1176px;
    margin-left: auto;
    padding-top: 10px;
    margin-right: auto;
    display: flex;
    align-items: center;
    justify-content: center;

    &.-with-gutter {
      max-width: 1224px;
      padding-left: 20px;
      padding-right: 20px;

      @media (min-width: 900px) {
        padding-left: 24px;
        padding-right: 24px;
      }
    }
  }
</style>
