<template>
  <v-card max-width="100%" class="mx-auto">
    <v-container>
      <v-row dense>
        <!-- <v-col cols="12">
          <v-card
            color="#385F73"
            dark
            v-for="(article, index) in articles"
            :key="index"
          >
            <v-card-title class="text-h5"> {{ article.title }} </v-card-title>
            <v-card-subtitle>{{ article.author }}</v-card-subtitle
            >s
            <v-card-actions>
              <v-btn text> View </v-btn>
            </v-card-actions>
          </v-card>
        </v-col> -->

        <v-col cols="12">
          <v-card
            dark
            class="text-h5"
            v-for="(article, index) in articles"
            :key="index"
            color= "#1F7087"
          >
            <div class="d-flex flex-no-wrap justify-space-between">
              <div>
                <v-card-title>{{ article.title }}</v-card-title>

                <v-card-subtitle> {{ article.author }}</v-card-subtitle>

                <v-card-actions> </v-card-actions>
              </div>

              <v-avatar class="ma-3" size="125" tile>
                <v-img
                  v-if="article.urlToImage"
                  :src="article.urlToImage"
                ></v-img>
              </v-avatar>
            </div>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-card>
</template>
<script>
import axios from "axios";

export default {
  data: () => {
    return {
      apiUrl: "",
      api_key: "8f50261d23f65ab9ae9af204e522017c",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "Chennai",
      weather: {},
      isBusy: false,
      showloader: false,
      currentPage: 1,
      totalResults: 0,
      maxPage: 20,
      searchword: "",
      articles: [],
      country: "us",
      alignments: ["center"],
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
          " https://newsapi.org/v2/everything?q=bitcoin&apiKey=aa52bfaecb834f22a28d38b26984e0dc"
        )
        .then((res) => {
          console.log("res", res);
          this.articles = res.data.articles;
        });
    },

    fetchWeather() {
      fetch(
        `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
      )
        .then((res) => {
          console.log("data", res);

          return res.json();
        })
        .then(this.setResults);
    },

    setResults(results) {
      this.weather = results;
      console.log("data", results);
    },

    currentDate() {
      const current = new Date();
      const date = `${current.getDate()}/${
        current.getMonth() + 1
      }/${current.getFullYear()}`;
      return date;
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
