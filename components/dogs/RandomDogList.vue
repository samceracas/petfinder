<template>
  <div>
    <h2 v-if="$fetchState.pending">Fetching doggos 🐶 <a-spin /></h2>
    <h3 v-if="$fetchState.error">
      We had trouble fetching your doggos 🐶😞 Please try again later...
    </h3>

    <a-button v-if="!$fetchState.pending" type="primary" @click="$fetch"
      >Fetch new batch of doggos!</a-button
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
        v-for="dog in dogList"
        :key="dog.id"
        hoverable
        style="width: 300px; margin: 25px 0"
      >
        <img slot="cover" :alt="dog.breeds[0].name" :src="dog.url" />
        <template
          v-if="!isUpvoted(dog.id) && !isDownvoted(dog.id)"
          slot="actions"
          class="ant-card-actions"
        >
          <a-tooltip>
            <template slot="title"> I like this doggo! </template>
            <a-button shape="circle" icon="up" @click="upvote(dog.id)" />
          </a-tooltip>
          <a-tooltip>
            <template slot="title"> I don't like this doggo :( </template>
            <a-button shape="circle" icon="down" @click="downvote(dog.id)" />
          </a-tooltip>
        </template>
        <a-card-meta :title="dog.breeds[0].name"> </a-card-meta>
      </a-card>
    </div>
  </div>
</template>
<script>
export default {
  async fetch() {
    this.dogs = await fetch(
      'https://api.thedogapi.com/v1/images/search?limit=20'
    ).then((res) => res.json())
    this.dogs = this.dogs.filter((dog) => {
      return dog.breeds.length > 0 && !this.isUpvoted(dog.id)
    })
  },

  data() {
    return {
      dogs: [],
      upvotes: [],
      downvotes: [],
    }
  },

  computed: {
    dogList() {
      return this.dogs.filter((dog) => !this.isDownvoted(dog.id))
    },
  },

  created() {
    this.upvotes = JSON.parse(localStorage.getItem('dog_upvotes')) || []
    this.downvotes = JSON.parse(localStorage.getItem('dog_downvotes')) || []
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
      localStorage.setItem('dog_upvotes', JSON.stringify(this.upvotes))
      this.$notification.open({
        message: 'Doggo upvoted!',
        icon: <a-icon type="smile" style="color: green" />,
      })
    },
    downvote(id) {
      if (this.isDownvoted(id)) return
      this.downvotes.push(id)
      localStorage.setItem('dog_downvotes', JSON.stringify(this.downvotes))
      this.$notification.open({
        message: 'Doggo downvoted :<',
        icon: <a-icon type="frown" style="color: red" />,
      })
    },
  },
}
</script>
