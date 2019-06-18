<template>
  <div id="app">
   
    <h1> Famous Quotes </h1>
    Search (by quote text): <input type="text" v-model="search"> <br><br>

    Showing quotes from <br>
    <input type="checkbox" value="movies" v-model="movieCheck" checked>
    <label for="movies"> Movies </label>

    <input type="checkbox" value="games" v-model="gameCheck" checked>
    <label for="games"> Games </label> <br>

    <button @click="prevPage" :disabled="page==1">
      Previous
    </button>
    <button @click="nextPage" :disabled="page == pageCount">
      Next
    </button>

    <div v-for="quoteContent in quoteFilter">
      <ul> 
        {{quoteContent.quote}} <br>
        -{{quoteContent.source}} ({{quoteContent.context}}) <hr>
      </ul>
    </div>


  </div>
</template>


<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>

<script>
var axios = require('axios');
  export default {
    data: function() {
      return {
        page: 1,
        pageCount: 3,
        quotes: [],
        search: '',
        movieCheck: true,
        gameCheck: true
      };
    },
    created: function() {
        axios
        .get("https://gist.githubusercontent.com/benchprep/dffc3bffa9704626aa8832a3b4de5b27/raw/quotes.json")
        .then(function(response) {
          console.log(response.data);
          this.quotes = response.data;
      }.bind(this));
    },

    methods: {
      nextPage(){ this.page++;},
      prevPage(){ this.page--;},

    },

    computed: {
      quoteFilter() {
        var filteredQuotes = this.quotes
        var search = this.search
        
        filteredQuotes = filteredQuotes.filter(function(content) {
          return content.quote.toLowerCase().includes(search.toLowerCase())
        });

        if (this.movieCheck == false)
          filteredQuotes = filteredQuotes.filter(function(content) {
            return content.theme != "movies";
          });
        if (this.gameCheck == false)
          filteredQuotes = filteredQuotes.filter(function(content) {
            return content.theme != "games";
          });

        var l = filteredQuotes.length;
        this.pageCount = Math.ceil(l/15);

        const start = (this.page -1) * 15,
            end = start + 15;
        filteredQuotes = filteredQuotes.slice(start, end);


        return filteredQuotes;
      },


    }
  };
</script>
