<template>
  <div class="box">
    <article class="media">
      <div class="media-content align-left">
        <div class="content">
          <p>
            <strong>{{this.$props.article.title}}</strong>
          </p>
          <div class="article-card-footer">
            <div class="footer-info">
              <div class="footer-info-item">
                <p>
                  Author: <strong>{{this.$props.article.by}}</strong>
                </p>
              </div>
              <div class="footer-info-item">
                <p>
                  likes:<strong>{{this.$props.article.score}}</strong>
                </p>
              </div>
              <div class="footer-info-item">
                <p>
                  posted: <strong> {{ this.$props.article.time }} </strong>
                </p>
              </div>

            </div>
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
    },

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
.article-card-footer {
  width: 100%;
  font-size: 12px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}
.footer-info {
  display: flex;
  flex-direction: row;
  /*align-items: flex-start;*/
}
.footer-info-item {
  padding-right: 20px;
}
</style>
