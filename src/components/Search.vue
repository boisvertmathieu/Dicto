<template>
  <div class="container">
    <form v-on:submit.prevent>
      <div class="form-group">
        <label for="input-word">Enter a word</label>
        <input type="text" class="form-control w-50 m-auto" id="input-word" v-model="inputword" />
      </div>
    </form>
    <hr />
    <div class="results py-2">
      <div v-if="err">
        <div class="error">{{ err }}</div>
      </div>
      <div v-else>
        <div class="row text-left">
          <div class="col">
            <h2 class="" v-if="entry">Results for : {{ entry }}</h2>
            <ul class="py-2 list-group" v-for="en in entries" :key="en.id">
              <div class="row">
                <div class="col">
                  <h4>Lexemes:</h4>
                  <ul class="list-group py-2" v-for="lex in en.lexemes" :key="lex.id">
                    <h5>
                      Part of speech: <strong>{{ lex.partOfSpeech }}</strong>
                    </h5>
                    <h5>Senses:</h5>
                    <ul class="list-group" v-for="sense in lex.senses" :key="sense.id">
                      <li class="list-group-item">
                        <h6>Definition:</h6>
                        <p>{{ sense.definition }}</p>
                        <div v-if="sense.usageExamples">
                          <h6>Usage examples:</h6>
                          <p>{{ sense.usageExamples[0] }}</p>
                        </div>
                      </li>
                    </ul>
                  </ul>
                </div>
              </div>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.error {
  color: red;
}
</style>

<script>
var debounce = require("debounce");
var axios = require("axios").default;
export default {
  name: "Search",
  data() {
    return {
      inputword: null,
      err: null,
      entry: null,
      entries: null,
    };
  },
  watch: {
    inputword: debounce(function() {
      this.fetchData();
    }, 1500),
  },
  methods: {
    fetchData: async function() {
      if (this.inputword != "" || typeof this.inputword != "undefined") {
        var options = {
          method: "GET",
          url: process.env.VUE_APP_URL + this.inputword,
          headers: {
            "x-rapidapi-key": process.env.VUE_APP_API_KEY,
            "x-rapidapi-host": process.env.VUE_APP_API_HOST,
          },
        };

        axios
          .request(options)
          .then(async (response) => {
            console.log(response.data);
            this.entries = response.data.entries;
            this.entry = this.inputword;
          })
          .catch((error) => {
            console.log(error);
            this.err = error;
          });
      }
    },
  },
};
</script>
