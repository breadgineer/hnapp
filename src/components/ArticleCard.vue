<template>
  <div class="box">
    <article class="media">
      <div class="media-content align-left">
        <div class="content">
          <p>
            <strong>{{this.$props.article.title}}</strong>
          </p>
          <div class="bottom">
            <p>
              <small> by: {{this.$props.article.by}} likes:{{this.$props.article.score}}  posted on {{ this.$props.article.time }}</small>
            </p>
            <a :href="this.$props.article.url">Read More</a>
          </div>
        </div>
      </div>
    </article>
  </div>
</template>

<script>

export default {
  name: 'ArticleCard',
  props: {
    article: {
      title: String,
      by: String,
      url: String,
      score: Number,
      time: String
    }
  }
}


</script>

<style scoped>
.box {
  width: 100%;
}
.align-left {
  text-align: left;
}
.bottom {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
</style>
