<template>
  <div>
    <v-container>
      <div>
        <div class="container">
          <div class="searchbar">
            <form @submit.prevent="fetchSearchNews">
              <input
                type="text"
                placeholder="search for latest news"
                v-model="searchword"
              />
            </form>

            <div class="search-icons">
              <i
                v-if="!isBusy"
                class="fas fa-search"
                @click="fetchSearchNews"
              ></i>
              <i v-else class="fas fa-spinner fa-spin"></i>
              <i class="fas fa-times" @click="fetchTopNews"></i>
            </div>
          </div>

          <div class="result-list">
            <article
              v-for="(article, index) in articles"
              :key="index"
              @click="navTo(article.url)"
            >
              <header>
                <img
                  v-if="article.urlToImage"
                  :src="article.urlToImage"
                  alt=""
                />
                <i v-else class="fas fa-image"></i>
              </header>
              <section v-html="article.title"></section>
              <footer>
                <i class="fas fa-chevron-right"></i>
              </footer>
            </article>
          </div>

          <div ref="infinitescrolltrigger" id="scroll-trigger">
            <i v-if="showloader" class="fas fa-spinner fa-spin"></i>
          </div>
        </div>
      </div>
    </v-container>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: ["apiKey"],
  data: () => {
    return {
      apiUrl: "",
      isBusy: false,
      showloader: false,
      currentPage: 1,
      totalResults: 0,
      maxPage: 20,
      searchword: "",
      articles: [],
      country: "us",
    };
  },
  computed: {
    pageCount() {
      return Math.ceil(this.totalResults / this.maxPerPage);
    },
  },
  methods: {
    navTo(url) {
      window.open(url);
    },

    resetData() {
      this.currentPage = 1;
      this.articles = [];
    },

    fetchSearchNews() {
      if (this.searchword !== "https://newsapi.org/v2/everything?q=") {
        this.appUrl =
          "" +
          this.searchword +
          "&pageSize=" +
          this.maxPerPage +
          "&apiKey=" +
          this.apiKey;
        this.isBusy = true;
        this.resetData();
        this.fetchData();
      } else {
        this.fetchTopNews();
      }
    },

    fetchTopNews() {
      this.apiUrl =
        "https://newsapi.org/v2/top-headlines?country=" +
        this.country +
        "" +
        "&pageSize=" +
        this.maxPerPage +
        "&apiKey=" +
        this.apiKey;
      this.resetData();
      this.searchword = "";
      this.resetData();
      this.fetchData();
    },

    fetchData() {
      axios
        .get(
          "https://newsapi.org/v2/top-headlines?country=us&category=business&apiKey=aa52bfaecb834f22a28d38b26984e0dc"
        )
        .then((res) => {
          console.log("res", res);
          this.articles = res.data.articles;
        });
    },
  },

  created() {
    this.fetchTopNews();
  },

  mounted() {
    // this.scrollTrigger();
  },
};
</script>

<style lang="scss" scoped>
.container {
  position: relative;

  .searchbar {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    width: 100%;
    height: 60px;
    font-size: 1.6rem;
    input {
      padding: 0 100px 0 20px;
      margin: 0;
      width: calc(100% - 120px);
      height: 60px;
      border: none;
      font-size: 2rem;
      color: #fff;
      background-color: #222;

      &:focus {
        outline: none;
      }
    }
    .search-icons {
      position: absolute;
      right: 20px;
      top: 20px;
      color: #fff;

      i {
        margin-left: 15px;
        cursor: pointer;
      }
    }
  }

  .result-list {
    padding-top: 60px;
  }

  article {
    display: flex;
    flex-direction: column;
    width: 280px;
    height: 320px;
    justify-content: center;
    background: rgba(26, 131, 218, 0.295);
    border: 1px solid #ddd;
    padding: 20px 20px;
    margin: 10px;
    color: rgb(255, 255, 255);
    margin-top: 25px;
    float: left;
    margin-left: 100px;
    overflow: hidden;
    cursor: pointer;

    header {
      display: flex;
      justify-content: center;
      align-items: center;
      overflow: hidden;

      img {
        align-self: center;
        max-width: 100%;
        height: auto;
      }

      i {
        font-size: 2rem;
      }
    }

    section {
      margin: 0;
      padding: 10px;
      height: 100px;
    }

    footer {
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 1.6rem;
      color: #888;
    }
  }

  #scroll-trigger {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100px;
    font-size: 1.6rem;
  }
}
</style>
