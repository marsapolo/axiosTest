<template>
  <div id="app">
    <img src="./assets/logo.png">
      <div class="container" id="app">
        <h3 class="text-center">Vue新闻</h3>
      </div>
      <!-- <div class="columns medium-3" v-for="result in results">
        <div class="card">
          <div class="card-divider">
            {{ result.title }}
          </div>
          <div class="card-section">
            <p>{{ result.abstract }}.</p>
          </div>
        </div>
      </div> -->
      <div class="row" v-for="posts in processedPosts">
        <div class="columns large-3 medium-6" v-for="post in posts">
          <div class="card">
            <div class="card-divider">
                {{ post.title }}
            </div>
            <a :href="post.url" target="_blank">
                <img :src="post.image_url">
            </a>
            <div class="card-section">
                <p>{{ post.abstract }}</p>
            </div>
          </div>
        </div>
      </div>
    <router-view/>
  </div>
</template>

<script>
const NYTBaseUrl = "https://api.nytimes.com/svc/topstories/v2/";
const Apikey = "ee13eb23c4d141ec9031630a3d02d41c";

function buildUrl(url) {
    return NYTBaseUrl + url + ".json?api-key=" + Apikey;
}

export default {
  name: 'app',
  data () {
    return {
      message: '123456',
      results: [ ]
    }
  },
  mounted() {
        // axios.get(".json?api-key=")
        // .then(response => {
        //     this.results = response.data.results})
        this.getPosts('home');
  },
  methods: {
        getPosts(section) {
            let url = buildUrl(section);
            axios.get(url).then((response) => {
                this.results = response.data.results;
            }).catch(error => {
                console.log(error);
            });
        }
  },
   computed: {
        processedPosts() {
            let posts = this.results;
            
            //添加image_url属性
            posts.map(post => {
                let imgObj = post.multimedia.find(media => media.format === "superJumbo");
                post.image_url = imgObj ? imgObj.url : "http://placehold.it/300x200?text=N/A";
            });
            
            //将数据分组
            let i, j, chunkedArray = [], chunk = 4;
            for (i = 0, j = 0; i < posts.length; i += chunk, j++) {
                chunkedArray[j] = posts.slice(i, i + chunk);
            }
            return chunkedArray;
        }
    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
