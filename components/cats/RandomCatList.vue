<template>
  <div>
    <h2 v-if="$fetchState.pending">Fetching cattos 🐱 <a-spin /></h2>
    <h3 v-if="$fetchState.error">
      We had trouble fetching your cattos 🐱😞 Please try again later...
    </h3>

    <a-button v-if="!$fetchState.pending" type="primary" @click="$fetch"
      >Fetch new batch of cattos!</a-button
    >
    <div
      :style="{
        display: 'flex',
        justifyContent: 'space-between',
        flexFlow: 'row wrap',
        alignItems: 'flex-start',
      }"
    >
      <a-card
        v-for="cat in catList"
        :key="cat.id"
        hoverable
        style="width: 300px; margin: 25px 0"
      >
        <img slot="cover" :alt="cat.breeds[0].name" :src="cat.url" />
        <template
          v-if="!isUpvoted(cat.id) && !isDownvoted(cat.id)"
          slot="actions"
          class="ant-card-actions"
        >
          <a-tooltip>
            <template slot="title"> I like this catto! </template>
            <a-button shape="circle" icon="up" @click="upvote(cat.id)" />
          </a-tooltip>
          <a-tooltip>
            <template slot="title"> I don't like this catto :( </template>
            <a-button shape="circle" icon="down" @click="downvote(cat.id)" />
          </a-tooltip>
        </template>
        <a-card-meta :title="cat.breeds[0].name"> </a-card-meta>
      </a-card>
    </div>
  </div>
</template>
<script>
export default {
  async fetch() {
    this.cats = await fetch(
      'https://api.thecatapi.com/v1/images/search?limit=100'
    ).then((res) => res.json())
    this.cats = this.cats.filter((cat) => {
      return cat.breeds.length > 0 && !this.isUpvoted(cat.id)
    })
  },

  data() {
    return {
      cats: [],
      upvotes: [],
      downvotes: [],
    }
  },

  computed: {
    catList() {
      return this.cats.filter((cat) => !this.isDownvoted(cat.id))
    },
  },

  created() {
    this.upvotes = JSON.parse(localStorage.getItem('cat_upvotes')) || []
    this.downvotes = JSON.parse(localStorage.getItem('downvotes')) || []
  },

  methods: {
    isUpvoted(id) {
      return this.upvotes.includes(id)
    },
    isDownvoted(id) {
      return this.downvotes.includes(id)
    },
    upvote(id) {
      if (this.isUpvoted(id)) return
      this.upvotes.push(id)
      localStorage.setItem('cat_upvotes', JSON.stringify(this.upvotes))
      this.$notification.open({
        message: 'Catto upvoted!',
        icon: <a-icon type="smile" style="color: green" />,
      })
    },
    downvote(id) {
      if (this.isDownvoted(id)) return
      this.downvotes.push(id)
      localStorage.setItem('downvotes', JSON.stringify(this.downvotes))
      this.$notification.open({
        message: 'Catto downvoted :<',
        icon: <a-icon type="frown" style="color: red" />,
      })
    },
  },
}
</script>
