<template>
  <div class="mb-4 tweetviewer">
    <div v-if="editMode" class="d-flex justify-content-between mb-2">
      <button class="btn btn-success" @click="$emit('toLeft')">&lt;&lt;</button>
      <button class="btn btn-danger" @click="$emit('doRemove')">Remove</button>
      <button class="btn btn-success" @click="$emit('toRight')">&gt;&gt;</button>
    </div>
    <div class="card mb-3" v-for="tweet in tweets" :key="tweet.id">
      <div class="card-body">
        <template v-if="tweet.retweeted_status">
          <h5 class="card-title"> <a :href="'https://twitter.com/'+tweet.retweeted_status.user.screen_name" target="_blank">{{tweet.retweeted_status.user.name}}</a> </h5>
          <h6 class="card-subtitle mb-2 text-muted"> <a :href="'https://twitter.com/'+tweet.retweeted_status.user.screen_name" target="_blank">@{{tweet.retweeted_status.user.screen_name}}</a> </h6>
        </template>
        <template v-else>
          <h5 class="card-title"> <a :href="'https://twitter.com/'+tweet.user.screen_name" target="_blank">{{tweet.user.name}}</a> </h5>
          <h6 class="card-subtitle mb-2 text-muted"> <a :href="'https://twitter.com/'+tweet.user.screen_name" target="_blank">@{{tweet.user.screen_name}}</a> </h6>
        </template>
        <h6 class="card-subtitle mb-2 text-muted">{{(new Date(tweet.created_at)).toLocaleDateString()}}</h6>
        <div v-if="false">{{tweet.id}}</div>
        <template v-if="!tweet.retweeted_status">
          <div>{{tweet.full_text}}</div>
          <a :href="tweet.entities.urls[0].url" v-if="tweet.entities.urls[0]" target="_blank" class="card-link">Link to content</a>
        </template>
        <template v-else>
          <div>{{tweet.retweeted_status.full_text}}</div>
          <a :href="tweet.retweeted_status.entities.urls[0].url" v-if="tweet.retweeted_status.entities.urls[0]" target="_blank" class="card-link">Link to content</a>
        </template>
        <a :href="'https://twitter.com/'+tweet.user.id+'/status/'+tweet.id_str" target="_blank" class="card-link">Link to tweet</a>
      </div>
    </div>
    <button class="btn btn-success w-100" @click="tenmore" :disabled="loading">{{loading?"Loading...":"Load 10 more tweets"}}</button>
  </div>
</template>

<script>
import Vue from 'vue';

export default {
  name: 'HelloWorld',
  props: {
    user: String,
    editMode: Boolean
  },
  data() {
    return {
      tweets: [],
      count: 30,
      loading: true
    }
  },
  methods: {
    reload() {
      $.get("http://localhost:7890/1.1/statuses/user_timeline.json?count="+this.count+"&screen_name="+this.user+"&tweet_mode=extended",(data) => {
        data = JSON.parse(data);
        //this.data.MakeSchool = data;
        Vue.set(this,"tweets",data);
        this.loading = false;
        //console.log(this);
      })
    },
    tenmore() {
      this.count += 10
      this.loading = true
      this.reload()
    }
  },
  created() {
    this.reload()
  }
}
</script>

<style scoped>

</style>