<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{
          name: 'EventList',
          query: { page: page - 1, pageSize: pageSize }
        }"
        rel="prev"
        v-if="page != 1"
        >Prev Page</router-link
      >
      <router-link
        id="page-next"
        :to="{
          name: 'EventList',
          query: { page: page + 1, pageSize: pageSize }
        }"
        rel="next"
        v-if="hasNextPage"
        >Next Page</router-link
      >
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { watchEffect } from '@vue/runtime-core'
// import axios from 'axios'
export default {
  name: 'EventList',
  props: {
    page: {
      type: Number,
      required: true
    },
    pageSize: {
      type: Number,
      required: true
    }
  },
  components: {
    EventCard // register it as a child component
  },
  data() {
    return {
      events: null,
      totalEvent: 0
    }
  },
  created() {
    watchEffect(() => {
      EventService.getEvents(this.pageSize, this.page)
        .then((response) => {
          this.events = response.data
          this.totalEvent = response.headers['x-total-count']
        })
        .catch((error) => {
          console.log(error)
        })
    })
  },
  computed: {
    hasNextPage() {
      let totalPages = Math.ceil(this.totalEvent / this.pageSize)
      return this.page < totalPages
    }
  }
}
</script>
<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pageination {
  display: flex;
  width: 290px;
}

.pageination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e40;
}

#page-prev {
  text-align: left;
}

#page-next {
  text-align: right;
}
</style>
