/* eslint-disable */


<template>
  <div id="app">
    <div class="container">
      <div class="card-columns">

        <div class="card p-3" v-for="(post, key) in posts">     
          <blockquote v-bind:key="index" class="card-blockquote">
            <p>
              {{ post.data.title }}
            </p>
            <footer>
              <small>
                <div><a target="_blank" :href="post.data.url">
                  Read more on {{ post.data.domain }}
                </a></div>
                <div><a target="_blank" :href="post.data.permalink">
                  <small>{{ post.data.num_comments }} Comments</small>
                </a></div>
              </small>
            </footer>
          </blockquote>

        </div>
      </div>
        
      <div class="text-center" v-show="postsLoading">
        Loading...
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'

  export default { 
    created () {
      this.getPosts()
      window.addEventListener('scroll', this.handleScroll)
    },

    data() {
      return {
        posts: [],
        postsLoading: false,
        nextPage: null
      }
    },

    methods: {
      getPosts (page) {
        this.postsLoading = true
        var url = 'https://www.reddit.com/r/all/top.json?limit=30&count=30'

        if (page != null) {
          url = 'https://www.reddit.com/r/all/top.json?limit=30&count=30&after=' + page
        }

        axios.get(url)
          .then(response => {
            this.posts = this.posts.concat(response.data.data.children)
            this.nextPage = response.data.data.after
            this.postsLoading = false
          })
          .catch(error => {
            console.log(error)
          })
      },

      handleScroll () {
        if (document.body.scrollHeight - window.innerHeight - document.documentElement.scrollTop <= 5) {
          if (this.nextPage != null) {
            this.getPosts(this.nextPage)
            this.nextPage = null;
          }
        }
      }
    }
  }
</script>