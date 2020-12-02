<template>
  <div class="m-container">
    <button class="arrow_style">
      <img src="@/assets/arrow-left-icon.svg" id="arrow_img" />
      <span>anterior</span>
    </button>
    <button class="arrow_style">
      <span>próxima</span>
      <img src="@/assets/arrow-right-icon.svg" id="arrow_img" />
    </button>
  </div>
</template>

<script>
export default {
  components: {},
  methods: {},
  data() {
    return {};
  },
};
</script>
<style  scoped>
#arrow_img {
  width: 25px;
  height: 18px;
}
.arrow_style {
  border: 1px solid #dedede;
  display: flex;
  align-items: center;
  margin: 10px;
  width: 100px;
  background: transparent;
  color: #F05B78;

}
.m-container {
  width: 100%;
  display: flex;
  justify-content: center;
}
</style>