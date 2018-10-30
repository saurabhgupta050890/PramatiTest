<template>
  <div>
    <div class="autocomplete">
      <input type="text" v-model="search" @input="onChange" @focus="showOptions"/>
      <button type="button" @click="clearSearch"> clear</button>
      <ul class="autocomplete-results" v-show="isOpen">
        <li class="autocomplete-result" @focus="showOptions" @blur="hideOptions" v-for="repos in results" :key="repos.id" @click="showResults(repos)">
          {{repos.full_name}}
        </li>
      </ul>
    </div>

    <div>
      <p> <a :href="selected.html_url"> {{selected.full_name }} </a> </p>
      <p>{{selected.description}}</p>
    </div>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  name: "HelloWorld",
  data() {
    return {
      search: "",
      results: [],
      loading: false,
      isOpen: false,
      selected: {}
    };
  },
  methods: {
    onChange: _.debounce(function() {
      this.selected = {};
      this.loading = true;
      this.isOpen = true;
      if (this.search.length > 3) {
        //this.loading = true;
        fetch(`https://api.github.com/search/repositories?q=${this.search}`)
          .then(response => response.json())
          .then(json => {
            this.loading = false;
            console.log(json);
            this.results = json.items;
          });
      } else {
        (this.results = []), (this.isOpen = false);
        this.loading = false;
      }
    }, 300),
    showResults(repo) {
      this.isOpen = false;
      this.selected = repo;
    },
    showOptions() {
      if(this.search && this.results.length > 0) {
        this.isOpen = true;
      }
    },
    hideOptions() {
      this.isOpen = false;
    },
    clearSearch() {
      this.search = "";
      this.results = [];
      this.isOpen = false;
    }
  },
  props: {
    msg: String
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

a {
  color: #42b983;
}

.autocomplete {
  position: relative;
  width: 200px;
}

.autocomplete-results {
  padding: 0;
  margin: 0;
  border: 1px solid #eeeeee;
  height: 300px;
  width: auto;
  overflow: auto;
}

.autocomplete-result {
  list-style: none;
  text-align: left;
  padding: 4px 2px;
  cursor: pointer;
}

.autocomplete-result:hover {
  background-color: #4aae9b;
  color: white;
}
</style>
