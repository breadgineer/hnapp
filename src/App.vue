<template v-if="articles" >
  <body>
    <article-card v-for="article in articles" :key="article.id" :article="article"/>
  </body>
</template>

<script>
import ArticleCard from "@/components/ArticleCard";

export default {
  name: 'App',
  components: {
    ArticleCard,
  },
  data() {
    return {
      articles: []

  }},
  methods: {
    async checkResponseOk(response) {
      if (!response.ok) {
        throw new Error(`HTTP error: ${response.status}`);
      }
    },

    async getNewArticleIds() {
      try {
        const response = await fetch("https://hacker-news.firebaseio.com/v0/newstories.json?print=pretty")
        await this.checkResponseOk(response)
        const articleIds = await response.json()
        return articleIds.slice(0,10)

      } catch (error){
        console.log(`Could not fetch article ids: ${error}`)
      }

    },

    async getNewArticles() {
      const urlHead = "https://hacker-news.firebaseio.com/v0/item/"
      const urlTail = ".json?print=pretty"
      const articleIds = await this.getNewArticleIds()
      const _articles = []

      try {
        for (const id of articleIds) {

          const response = await fetch(`${urlHead}${id}${urlTail}`)
          await this.checkResponseOk(response)
          const article = await response.json()

          _articles.push(article)

        }
        this.articles.push(..._articles)
      } catch (error){
        console.log(`Could not fetch articles: ${error}`)
      }
    },

  },
  async mounted() {
      await this.getNewArticles()
  }
}
</script>

<style>

#app {

  margin: 0 auto;
  padding: 2rem;
  text-align: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;

}
body {

  display: flex;
  flex-direction: column;
  place-items: center;
  min-width: 320px;
  min-height: 100vh;
}
</style>
