<template>
  <div class="container">
    <div
      v-if="men || women"
      class="tile-background"
      :class="{ menSection: men, womenSection: women }"
    ></div>
    <div v-else class="default-background"></div>
    <div class="section">
      <div v-if="loading" class="wrapper">
        <div class="loader"></div>
      </div>
      <div
        v-else-if="Object.keys(data).length == 0 && loading == false"
        class="wrapperNoProduct"
      >
        <div style="display: flex; gap: 200px; margin-top: 100px">
          <img src="../assets/images/aliskiri.png" />
          <img src="../assets/images/aliskanan.png" />
        </div>
        <div>
          <img src="../assets/images/mata.png" />
        </div>
        <div style="margin-top: 50px">
          <img src="../assets/images/mulut.png" />
        </div>
        <div style="position: absolute; top: auto">
          <div style="display: flex; flex-direction: column">
            <p>This Product is unavailable to show</p>
            <button class="defaultButton" @click="handleClick(1)">
              Next product
            </button>
          </div>
        </div>
      </div>
      <div v-else class="wrapper">
        <div class="image">
          <img :src="data.image" width="300" />
        </div>
        <div class="content" :class="{ menColor: men, womenColor: women }">
          <div class="content-body">
            <h3>{{ data.title }}</h3>
            <div
              style="
                display: flex;
                justify-content: space-between;
                padding: 20px 0;
              "
            >
              <span>{{ data.category }}</span>
              <div class="rating">
                <span>{{ data.rating.rate }}/5</span>
                <svg
                  v-for="i in roundedRate"
                  :key="i"
                  xmlns="http://www.w3.org/2000/svg"
                  width="18"
                  height="18"
                  viewBox="0 0 18 18"
                  fill="none"
                >
                  <circle cx="9" cy="9" r="9" fill="currentColor" />
                </svg>
                <svg
                  v-for="i in emptyRate"
                  :key="i + 5"
                  xmlns="http://www.w3.org/2000/svg"
                  width="18"
                  height="18"
                  viewBox="0 0 18 18"
                  fill="none"
                >
                  <circle
                    cx="9"
                    cy="9"
                    r="8.5"
                    fill="white"
                    stroke="currentColor"
                  />
                </svg>
              </div>
            </div>
            <p v-if="data.description.length > 500">
              {{ data.description.slice(0, 500) }}...
            </p>
            <p v-else>{{ data.description }}</p>
          </div>
          <div class="content-footer">
            <h3>${{ data.price }}</h3>
            <div class="navigation">
              <button :class="{ menButton: men, womenButton: women }">
                Buy now
              </button>
              <button
                :class="{ menButton: men, womenButton: women }"
                @click="handleClick(1)"
              >
                Next product
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ProductDisplay",
  props: {
    msg: String,
  },
  data() {
    return {
      id: 1,
      data: {},
      men: false,
      women: false,
      roundedRate: null,
      emptyRate: null,
      error: true,
      loading: false,
    };
  },

  mounted() {
    this.loading = false;
    this.fetchProduct(this.id);
  },

  methods: {
    async fetchProduct(id) {
      const url = "https://fakestoreapi.com/products/";
      try {
        this.loading = true;
        const res = await fetch(url + id);
        const result = await res.json();
        if (
          result.category == "men's clothing" ||
          result.category == "women's clothing"
        ) {
          switch (result.category) {
            case "men's clothing":
              this.men = true;
              this.women = false;
              break;
            case "women's clothing":
              this.women = true;
              this.men = false;
          }
          this.data = result;
          this.roundedRate = Math.round(result.rating.rate);
          this.emptyRate = 5 - this.roundedRate;
        } else {
          this.data = {};
          this.men = false;
          this.women = false;
        }
        this.loading = false;
      } catch (error) {
        this.error = error;
      }
    },

    handleClick(param) {
      if (this.id == 1 && param == -1) {
        this.id = 20;
      } else if (this.id == 20 && param == 1) {
        this.id = 1;
      } else {
        this.id += param;
      }
      this.fetchProduct(this.id);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
