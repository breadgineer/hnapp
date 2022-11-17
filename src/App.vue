<template>
  <div class="page-content">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Koulen&display=swap" rel="stylesheet">
    <div class="header">
      <p class="page-title">Hacker News Feed</p>
    </div>
    <div class="body">
      <spinner v-show="fetchingArticles"></spinner>
      <article-card v-for="article in articles" :key="article.id" :article="article"/>
    </div>
  </div>

</template>

<script>
import ArticleCard from "@/components/ArticleCard";
import Spinner from 'vue-simple-spinner'

export default {
  name: 'App',
  components: {
    ArticleCard,
    Spinner,
  },
  data() {
    return {
      articles: [],
      fetchingArticles: true,

  }},
  methods: {
    async checkResponseOk(response) {
      if (!response.ok) {
        throw new Error(`HTTP error: ${response.status}`);
      }
    },

    async fetchNewArticleIds() {
      try {
        const response = await fetch("https://hacker-news.firebaseio.com/v0/newstories.json?print=pretty")
        await this.checkResponseOk(response)
        const articleIds = await response.json()
        return articleIds.slice(0,10)

      } catch (error){
        console.log(`Could not fetch article ids: ${error}`)
      }

    },

    async fetchNewArticles() {
      const urlHead = "https://hacker-news.firebaseio.com/v0/item/"
      const urlTail = ".json?print=pretty"
      const articleIds = await this.fetchNewArticleIds()
      const _articles = []

      try {
        for (const id of articleIds) {

          const response = await fetch(`${urlHead}${id}${urlTail}`)
          await this.checkResponseOk(response)
          const article = await response.json()
          article.time = this.getPostDate(article)
          _articles.push(article)

        }
        this.articles = _articles
        this.setArticlesInBrowser(_articles)


      } catch (error){
        console.log(`Could not fetch articles: ${error}`)
      }
    },

    getPostDate(article) {
        let time = new Date(article.time * 1000)
        let today = new Date()
        if (time.getDate() === today.getDate()){
          return `Today at ${time.getHours()}:${time.getMinutes()}`
        } else {
          return `${time.getMonth()}.${time.getDate()}.${time.getFullYear()}`
        }
      },


    setArticlesInBrowser(data) {
      const fetchedArticles = {
        timestamp: Date.now(),
        articles: data
      }
      localStorage.setItem("fetchedArticles",JSON.stringify(fetchedArticles))
    },

    getArticlesFromBrowser() {
      return JSON.parse(localStorage.getItem("fetchedArticles"))

    },

    renderStoredArticles() {
      if (localStorage.fetchedArticles) {
        const storedArticles = this.getArticlesFromBrowser()
        this.articles = storedArticles.articles
      }
    },

    async getArticles() {
      const refreshTime = 120000
      const now = Date.now()
      const lastFetchTime = this.getArticlesFromBrowser().timestamp
      const timeSinceLastFetch = now - lastFetchTime
      const timeUntilNextRefreshCall = (refreshTime - timeSinceLastFetch)
      console.log(`${timeUntilNextRefreshCall/1000}s until the next API call`)
      const fetchNewArticles = timeSinceLastFetch > refreshTime
      if (!fetchNewArticles || timeUntilNextRefreshCall > 0 ) {
        setTimeout(()=> this.getArticles(),timeUntilNextRefreshCall);
      } else {
        await this.fetchNewArticles();
        setTimeout(()=> this.getArticles(),refreshTime);
      }

    },

  },
  async mounted() {
      if (!this.getArticlesFromBrowser()){
        await this.fetchNewArticles()
      }
      this.fetchingArticles = false
      this.renderStoredArticles();
      await this.getArticles()

  }
}
</script>

<style>


.page-content{
  margin:auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

}

.header {
  padding-top: 40px;
  margin-bottom: 40px;
  text-align: center;
  background-color: #ffffff;
  width: 100%;
  position: fixed;
  top: 0;
}
.body {
  margin: 20px 0px;
  padding: 120px 20px 0;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  width: 700px;

}
.page-title {
  font-family: 'Koulen', cursive;
  font-size: 60px;
}
</style>
