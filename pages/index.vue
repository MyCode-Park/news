<template>
  <v-container>
    <div class="container">
      <v-section
        v-for="align in alignments"
        :key="align"
        class="lighten-5 mb-6"
      >
        <h1 class="headline">Headlines</h1>
        <v-row no-gutters>
          <v-col>
            <v-card class="head-row" max-width="100%">
              <v-banner class="">
                <v-col>
                  <v-card-subtitle class="pb-1 headlineBanner">
                    <ul v-for="(article, index) in articles" :key="index">
                      <li>
                        <div class="linkers" max-width="100%">
                          <a
                            class="links"
                            flat
                            :href="article.url"
                            target="_blank"
                          >
                            {{ article.title }}
                          </a>
                          <br />
                          {{ article.publishedAt }}
                        </div>
                      </li>
                    </ul>
                  </v-card-subtitle>
                </v-col>
                <template v-slot:actions>
                  <v-row justify="center">
                    <v-btn text color="primary"> View Full coverage </v-btn>
                  </v-row>
                </template>
              </v-banner>
            </v-card>
          </v-col>
          <v-col>
            <v-card class="mx-auto" max-width="80%">
              <v-text-field
                label="Search for Weather"
                placeholder="Search here"
                outlined
                value="Chennai"
                v-model="query"
                @keypress="fetchWeather"
              ></v-text-field>
              <v-section v-if="typeof weather.main != 'undefined'">
                <v-list-item two-line>
                  <v-list-item-content>
                    <v-list-item-title class="text-h5">
                      {{ weather.name }}, {{ weather.sys.country }}
                    </v-list-item-title>
                    <v-list-item-subtitle
                      >{{ currentDate()  }},
                      {{ weather.weather[0].main }}</v-list-item-subtitle
                    >
                  </v-list-item-content>
                </v-list-item>

                <v-card-text>
                  <v-row align="center">
                    <v-col class="text-h2" cols="6">
                      {{ Math.round(weather.main.temp) }}&deg;C
                    </v-col>
                    <v-col cols="6">
                      <v-img
                        src="https://cdn.vuetifyjs.com/images/cards/sun.png"
                        alt="Sunny image"
                        width="92"
                      ></v-img>
                    </v-col>
                  </v-row>
                </v-card-text>

                <v-list-item>
                  <v-list-item-icon>
                    <v-icon>mdi-send</v-icon>
                  </v-list-item-icon>
                  <v-list-item-subtitle
                    >{{ weather.wind.speed }} km/h</v-list-item-subtitle
                  >
                </v-list-item>

                <v-list-item>
                  <v-list-item-icon>
                    <v-icon>mdi-cloud-download</v-icon>
                  </v-list-item-icon>
                  <v-list-item-subtitle
                    >{{ weather.main.humidity }}%</v-list-item-subtitle
                  >
                </v-list-item>
                <v-list class="transparent">
                  <v-list-item v-for="item in forecast" :key="item.day">
                    <v-list-item-title>{{ item.day }}</v-list-item-title>

                    <v-list-item-icon>
                      <v-icon>{{ item.icon }}</v-icon>
                    </v-list-item-icon>

                    <v-list-item-subtitle class="text-right">
                      {{ "weather.main.temp" }}
                    </v-list-item-subtitle>
                  </v-list-item>
                </v-list>
              </v-section>
            </v-card>
          </v-col>
        </v-row>
      </v-section>
    </div>
  </v-container>
</template>

<script>
import axios from "axios";
import moment from 'moment';

export default {
  name: "InspirePage",

  props: ["apiKey"],
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
          "https://newsapi.org/v2/top-headlines?country=us&category=business&apiKey=aa52bfaecb834f22a28d38b26984e0dc"
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

<style lang="scss" scoped>
.theme--dark.v-application {
}
.headline {
  color: white;
  margin-bottom: 25px;
}

v-banner > v-banner__content {
  min-width: fit-content !important;
}

.container {
  // background: white;
  position: relative;
  .head-row {
    background: transparent;
    border: 1px solid #ddd;
    border-radius: 10px;
  }

  li {
    color: red;
  }
  .linkers {
    margin-bottom: 25px;
    color: rgb(86, 134, 224);
    a {
      color: white;
      font-size: 18px;
      text-decoration: none;
    }

    :hover {
      text-decoration: underline;
      color: crimson;
    }
  }
}
</style>
