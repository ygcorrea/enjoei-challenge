<template>
  <div style="position: relative">
    <a class="c-product-card" :href="href">
      <img class="c-product-card__image" :src="imageUrl" />
    </a>
    <div class="img-card">
      <div id="original-price" v-if="originalPrice == currentPrice">
        R${{ currentPrice }}
      </div>
      <div id="promo-price" v-else>
        R${{ currentPrice }}
        <a> R${{ originalPrice }} </a>
      </div>
    </div>
    <div v-if="originalPrice != currentPrice" class="discount-card">
      <div id="discount-value">{{ discount }}%</div>
    </div>
  </div>
</template>

<script>
import { getImageUrl, IMAGE_PRESETS } from "@/tools/get-image-url";

export default {
  props: {
    imageId: {
      type: String,
      required: true,
    },
    title: {
      type: String,
      required: true,
    },
    path: {
      type: String,
      required: true,
    },
    currentPrice: {
      type: Number,
      required: false,
    },
    originalPrice: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      discount: null,
      imageUrl: getImageUrl(this.imageId, IMAGE_PRESETS.product.card),
    };
  },
  computed: {
    href() {
      return `https://www.enjoei.com.br/p/${this.path}`;
    },
  },

  methods: {
    calcOfDiscount() {
      this.discount =
        ((this.originalPrice - this.currentPrice) / this.originalPrice).toFixed(
          2
        ) * 100;
    },
  },
  mounted() {
    this.calcOfDiscount();
  },
};
</script>

<style scoped lang="scss">
.c-product-card {
  display: block;
}
.img-card {
  position: absolute;
  display: flex;
  background: #fff;
  border-radius: 3px;
  min-width: 50px;
  height: 22px;
  align-items: center;
  justify-content: center;
  top: 192px;
  left: 12px;
  font-size: 0.9em;
}
.discount-card {
  position: absolute;
  display: flex;
  background: #f05b81;
  border-radius: 3px;
  min-width: 50px;
  height: 22px;
  align-items: center;
  justify-content: center;
  top: 2px;
  left: 175px;
  font-size: 0.9em;
}
@media (max-width: 600px) {
  .discount-card {
    top: 2px;
    left: 215px;
  }
  .img-card {
    top: 192px;
    left: 55px;
  }
}
#discount-value {
  color: #fff;
}
#original-price {
  color: #000;
}
#promo-price {
  margin-left: 10px;
}
#promo-price a {
  color: #868585;
  text-align: center;
  text-decoration: line-through;
  padding: 10px;
}
</style>
