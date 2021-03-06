<template>
  <div class="article-list" :class="{mobile: mobileFlag}">
    <div class="article-wrapper" v-for="(article, i) in articleList" :key="i">
      <div class="text">
        <div class="title" @click="goDetail(article)">{{ article.title }}</div>
        <div class="message">
          <div class="msg time">{{ article.createTime }}</div>
          <div class="msg view">
            <icon-font icon="icon-view">
              <span slot="content">{{ article.view }}</span>
            </icon-font>
          </div>
        </div>
        <div class="markdown-body markdownSummary" v-html="article.summary"></div>
        <div class="readall" @click="goDetail(article)">阅读全文 »</div>
      </div>
      <div class="picture" v-if="!mobileFlag">
        <img v-lazy="`https://source.unsplash.com/200x200/weekly?water,${i}`" alt="pic">
      </div>
    </div>
    <el-pagination
      v-if="pageShow"
      background
      layout="prev, pager, next"
      :total="1000">
    </el-pagination>
  </div>
</template>

<script>
  import IconFont from '@/components/Iconfont'
  import { apiUrl } from '@/serviceAPI.config.js'
  import { dateFormat } from '@/common/js/tool.js'
  import { mapGetters } from 'vuex'
  export default {
    name: 'articleList',
    data() {
      return {
        articleList: [],
        pageShow: false
      }
    },
    created() {
      this.getArticle()
    },
    computed: {
      ...mapGetters([
        'mobileFlag'
      ])
    },
    methods: {
      getArticle() {
        this.$http.post(apiUrl.getArticle, {
          data: {summary: true}
        }).then(res => {
          if (res.data.code === 200) {
            this.articleList = res.data.message
            this.articleList.forEach(ele => {
              ele.createTime = dateFormat(ele.createTime, 'yyyy-MM-dd')
            })
          } else {
            this.$message.error(res.data.message)
          }
        })
      },
      goDetail(article) {
        this.$router.push(`/article/${article._id}`)
      }
    },
    components: {
      IconFont
    }
  }
</script>

<style lang="scss" scoped>
  @import '@/common/css/variable.scss';
  .article-list {
    width: 70%;
    margin: 0 auto;
    .article-wrapper {
      display: flex;
      background: $color-white;
      box-shadow: 0 0 5px 5px rgba(0,0,0,.01);
      padding: 20px;
      margin-bottom: 20px;
      overflow: hidden;
      border-radius: 4px;
      .text {
        text-align: left;
        margin-right: 10px;
        width: 100%;
        position: relative;
        .title {
          font-weight: bold;
          font-size: 22px;
          color: $color-black;
          margin-bottom: 5px;
          &:hover {
            cursor: pointer;
            text-decoration: underline;
          }
        }
        .message {
          display: flex;
          color: $color-gray;
          font-size: 14px;
          line-height: 16px;
          .msg {
            margin-right: 10px;
          }
        }
        .readall {
          position: absolute;
          bottom: 0;
          right: 0;
          font-size: 14px;
          background: none;
          border: none;
          border-bottom: 2px solid #666;
          line-height: 2;
          padding: 0 3px;
          cursor: pointer;
          color: #555;
          &:hover {
            color: $color-black;
            border-bottom: 2px solid $color-black;
          }
        }
      }
      .picture {
        position: relative;
        width: 200px;
        height: 200px;
      }
    }
    &.mobile {
      width: 100%;
      padding-top: 50px;
      .article-wrapper {
        background: $color-white;
        margin: 10px 0;
      }
      .markdownSummary {
        p {
          margin: 0 !important;
          padding: 0 !important;
        }
      }
    }
  }
</style>
