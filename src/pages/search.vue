<template>
  <div class="main-container">
    <div class="header">
      <div class="header-actions">
        <div v-if="!products.length" class="search-result">
          nenhum produto encontrado
        </div>
        <div v-else-if="term.length" class="search-result">
          {{ products.length }} produtos encontrados
        </div>
      </div>

      <div class="icon-input-container">
        <div class="hidden-button-xs">
          <button @click="clearFilter()" class="clear-btn">limpar busca</button>
        </div>
        <input
          placeholder="buscar"
          v-model="term"
          type="text"
          class="icon-input"
          id="input"
          @keypress.enter="searchProducts()"
        />
        <div class="icon-container">
          <div @click="searchProducts()" class="icon-search" />
        </div>
      </div>
      <div class="hidden-button-lg">
        <button @click="clearFilter()" class="clear-btn">limpar busca</button>
      </div>
    </div>

    <div v-if="products.length" class="image-container">
      <a
        v-for="product in products"
        :key="product.id"
        :href="`https://www.enjoei.com.br/p/${product.path}`"
      >
        <vProductCard
          :imageId="product.photo.image_public_id"
          :path="product.path"
          :title="product.title.name"
          :currentPrice="product.price.current"
          :originalPrice="product.price.original"
        />
      </a>
    </div>

    <div v-else class="c-not-found">
      <div class="not-found">
        <p>ué, não econtramos nadinha</p>
        <span>que tal recomeçar do começo?</span>
        <button @click="clearFilter()" class="not-found-clear">
          limpar busca
        </button>
      </div>
      <div>
        <img
          class="image-not-found"
          src="@/assets/blank-slate.png"
          alt="notFound"
        />
      </div>
    </div>
    <div v-if="products.length" class="pagination-container">
      <div class="pagination">
        <button @click="prevPage" class="arrow_style">
          <img src="@/assets/arrow-left-icon.svg" id="arrow_img" />
          <span>anterior</span>
        </button>
        <button @click="nextPage" class="arrow_style">
          <span>próxima</span>
          <img src="@/assets/arrow-right-icon.svg" id="arrow_img" />
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import vProductCard from "@/components/product-card.vue";

export default {
  components: {
    vProductCard,
  },
  data() {
    return {
      pageNumber: 1,
      size: 10,
      products: [],
      after: "",
      historyPages: [""],
      term: "",
    };
  },
  methods: {
    searchProducts() {
      axios
        .post("https://enjusearch.enjoei.com.br/graphql-search", {
          query: `
            query searchProducts($term: String!, $first: Int, $after: String) {
              search(products: {term: $term}) {
                products(first: $first, after: $after) {
                  total
                  edges {
                    cursor
                    node {
                      id
                      path
                      photo {
                        image_public_id
                      }
                      title {
                        name
                      }
                      brand {
                        displayable_name
                      }
                      price {
                        original
                        current
                      }
                    }
                  }
                }
              }
            }
            `,
          variables: {
            term: this.term,
            first: 10,
            after: this.after,
          },
        })
        .then((res) => {
          const productsList = res.data.data.search.products.edges.map(
            (edge) => {
              const product = edge.node;

              product.cursor = edge.cursor;

              return product;
            }
          );
          this.products = productsList;
        });
    },
    clearFilter() {
      this.$nextTick(() => {
        this.term = "";
        this.searchProducts();
      });
    },
    nextPage() {
      const lastProductCursor = this.products[this.products.length - 1].cursor;

      this.after = lastProductCursor;

      this.searchProducts();

      this.historyPages.push(this.after);

      this.pageNumber++;
    },
    prevPage() {
      if (this.pageNumber > 1) {
        this.historyPages.splice(-1);
      }

      if (this.pageNumber === 2) {
        this.after = "";
      } else {
        const previousHistoryCursor = this.historyPages[
          this.historyPages.length - 1
        ];
        this.after = previousHistoryCursor;
      }

      if (this.pageNumber > 1) {
        this.pageNumber--;
      }

      this.searchProducts();
    },
  },

  created() {
    this.searchProducts();
  },
};
</script>
<style >
.c-not-found {
  flex-direction: column;
  align-items: center;
  display: flex;
}
.image-not-found {
  width: 420px;
  height: 240px;
  margin-top: 50px;
}
.not-found {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.not-found p {
  font-size: 1.6em;
  font-weight: bold;
}
.not-found span {
  font-weight: 100;
  font-size: 1em;
}
.not-found-clear {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 40px;
  height: 48px;
  width: 140px;
  background: #f05b78;
  outline: none;
  border: transparent;
  border-radius: 3px;
  cursor: pointer;
  color: #fff;
}
.main-container {
  width: 100vw;
}
.header {
  display: flex;
  align-items: baseline;
}
.header-actions {
  width: 100%;
}

.search-result {
  font-weight: bold;
  width: 100%;
}

.search-result {
  font-weight: bold;
  width: 100%;
}
.image-container {
  text-align: center;
  display: grid;
  width: 100%;
  grid-template-rows: 35vh 40vh;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
}
.image-container span {
  color: black;
  display: flex;
  height: 50px;
  font-size: 0.9em;
  align-items: center;
  justify-content: center;
}
.image-container img {
  max-width: 216px;
  height: 216px;
  border-radius: 5px;
}
.clear-btn {
  color: #f05b78;
  margin: 10px;
  border: none;
  outline: none;
  background-color: inherit;
  font-size: 16px;
  cursor: pointer;
  display: inline-block;
}
.clear-btn:hover {
  background: #eee;
}
.icon-input {
  border: 1px solid #e6e6e6;
  border-radius: 3px;
  margin: 8px 0;
  outline: none;
  padding: 8px 0px 8px 8px;
  transition: 0.3s;
  padding-right: 30px;
  height: 40px;
  width: 240px;
}

.pagination-container {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
#arrow_img {
  width: 25px;
  height: 18px;
}
.arrow_style {
  border: 1px solid #dedede;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 10px;
  height: 48px;
  width: 132px;
  background: transparent;
  outline: none;
  cursor: pointer;
  color: #f05b78;
}
.pagination {
  width: 100%;
  display: flex;
  justify-content: center;
}

@media (min-width: 601px) {
  .icon-input-container {
    width: 100%;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    position: relative;
  }
  .icon-search {
    height: 30px;
    background: url("../assets/search-icon.svg") no-repeat;
    background-size: contain;
    cursor: pointer;
  }

  .icon-container {
    position: absolute;
    top: 15px;
    right: 10px;
    width: 24px;
  }
  .hidden-button-lg {
    display: none;
  }
}
@media (max-width: 600px) {
  .header-actions {
    display: none;
  }
  .hidden-button-xs {
    display: none;
  }
  .icon-input-container {
    justify-content: center;
    align-items: center;
  }
  .icon-input-container {
    width: 100%;
    display: flex;
    justify-content: flex-start;
    align-items: center;
    position: relative;
  }
  .icon-search {
    height: 30px;
    background: url("../assets/search-icon.svg") no-repeat;
    background-size: contain;
    cursor: pointer;
  }

  .icon-container {
    position: absolute;
    top: 15px;
    right: 12px;
    width: 24px;
  }
  .clear-btn {
    font-size: 0.8em;
    height: 62px;
    width: 85px;
  }
}
</style>