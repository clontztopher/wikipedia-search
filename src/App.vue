<template>
  <div id="app">
    <div class="container">
      <h3 id="title" class="text-center">Wikipedia Search</h3>
      <div id="search-container" :class="{results: searchData.length > 0}">
        <form @submit.prevent="executeSearch">
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <button class="btn btn-success" type="submit">Search</button>
            </div>
            <input v-model="searchKey" type="text" class="form-control" placeholder="" aria-label="" aria-describedby="basic-addon1">
          </div>
        </form>
      </div>
      <div v-if="searchData.length" id="results-container">
        <div 
          class="result" 
          v-for="data in searchData" 
          v-bind:key="data.pageid"
          @click="openPage(data.title)">
          <p class="lead text-primary font-weight-bold">{{data.title}}</p>
          <p><small>Snippet</small></p>
          <p v-html="data.snippet"></p>
          <p>
            <small class="font-weight-bold">Article Word Count</small>&ensp;<span class="badge badge-warning">{{data.wordcount}}</span>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: () => {
    return {
      searchKey: '',
      searchData: [],
      uri: 'https://en.wikipedia.org/w/api.php',
      params: {
        action: 'query',
        list: 'search',
        format: 'json',
        origin: '*'
      }
    }
  },
  methods: {
    executeSearch(e) {
      if (!this.searchKey) return
      let params = Object.assign({}, this.params, { srsearch: this.searchKey })
      let uri = this.uri
      this.$axios
        .get(uri, { params: params })
        .then(data => (this.searchData = data.data.query.search))
        .catch(err => console.warn(err.message))
    },
    openPage(title) {
      window.open('https://en.wikipedia.org/wiki/' + title, '_blank')
    }
  }
}
</script>

<style lang="scss" scoped>
#app {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  overflow-y: scroll;
  .container {
    height: 100%;
  }
}
#title {
  padding: 2em;
}
#search-container {
  height: 300px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  flex-grow: 1;
  transition: height 400ms;
  &.results {
    height: 65px;
  }
}
#results-container {
  border-top: 4px solid #187aa3;
  background: white;
  padding: 10px;
  margin-bottom: 20px;
  p {
    margin-bottom: 10px;
  }
}
.result {
  padding: 20px 0;
  &:hover {
    cursor: pointer;
  }
  &:not(:last-of-type) {
    border-bottom: 4px solid #98d6f0;
  }
}
h3 {
  color: white;
}
</style>
