<template>
  <div class="flex">
    <div class="w-full py-4 lg:pt-4 lg:pb-4 dark:border-gray-800 lg:border-r">
      <div class="lg:px-8">
        <div
          v-for="article in articles.slice(getStart, getCurrent)"
          :key="article.slug"
          class="mt-4 font-midium text-gray-600 dark:text-gray-500 hover:text-gray-800 dark-hover:text-gray-100"
        >
          <nuxt-link
            :to="article.slug"
          >{{ article.title + ' (' + $dayjs(article.date).format('YYYY/MM/DD') + ')' }}</nuxt-link>
        </div>

        <div class="mt-5">
          <button
            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-1 border border-blue-700 rounded"
            v-show="hasPrev"
            :to="`?page=${getPrev}`"
            @click="clickCallback(getPrev)"
          >前のページ</button>

          <button
            class="bg-gray-500 text-white font-bold py-1 px-1 border border-gray-700 rounded opacity-50 cursor-not-allowed"
            v-show="!hasPrev"
          >前のページ</button>

          <p
            style="display: inline-flex; margin-left: 5px; margin-right: 5px;"
          >{{currentPage}} / {{Math.ceil(articles.length / this.perPage)}}</p>

          <button
            class="bg-blue-500 text-white hover:bg-blue-700 font-bold py-1 px-1 border border-blue-700 rounded"
            v-show="(this.currentPage < Math.ceil(articles.length / this.perPage))"
            :to="`?page=${getNext}`"
            @click="clickCallback(getNext)"
          >次のページ</button>

          <button
            class="bg-gray-500 text-white font-bold py-1 px-1 border border-gray-700 rounded opacity-50 cursor-not-allowed"
            v-show="!(this.currentPage < Math.ceil(articles.length / this.perPage))"
          >次のページ</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
 async asyncData({ params, query, error, $content }) {
    const articles = await $content("articles" || "index")
      .sortBy("pos", "desc")
      .fetch();

    return {
      currentPage: query["page"] === undefined ? 1 : Number(query["page"]),
      articles
    };
  },
  data() {
    return {
      perPage: 20
    };
  },
  head() {
    return {
      title: "ホーム"
    };
  },
  computed: {
    getCurrent: function() {
      return this.currentPage * this.perPage;
    },

    getStart: function() {
      let current = this.currentPage * this.perPage;
      return current - this.perPage;
    },

    getPrev: function() {
      return this.currentPage - 1;
    },

    getNext: function() {
      return this.currentPage + 1;
    },

    hasPrev: function() {
      return this.currentPage > 1;
    }
  },
  methods: {
    clickCallback(page) {
      this.currentPage = Number(page);
      this.$router.push(`?page=${page}`);
    }
  }
};
</script>
