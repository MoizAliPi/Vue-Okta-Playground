<template>
  <div class="container-fluid mt-4">
    <h1 class="h1">Posts Manager</h1>
    <b-alert :show="loading" variant="info">Loading...</b-alert>
    <b-row>
      <b-col>
        <div class="post-card" >
          <div class="card-body" v-for="post in posts" :key="post.id">
            <b-card 
              :title="post.title"
              :sub-title="post.updatedAt"
              :img-src="post.imageUrl"
              img-alt="Image"
              img-top
              tag="article"
              style="max-width: 20rem;"
              class="mb-2"
            >
            <b-card-text>
              {{post.body}}
            </b-card-text>
            <div class="text-right" v-if="userName != 'John Doe'">
                <b-button variant="secondary" href="#" @click.prevent="populatePostToEdit(post)">Edit</b-button>
                <b-button variant="danger" href="#" @click.prevent="deletePost(post.id)">Delete</b-button>
            </div>
            </b-card>
          </div>
        </div>
      </b-col>
      <b-col lg="3" >
        <b-card :title="(model.id ? 'Edit Post ID#' + model.id : 'New Post')" v-if="userName != 'John Doe'">
          <form @submit.prevent="savePost">
            <b-form-group label="Title">
              <b-form-input type="text" v-model="model.title"></b-form-input>
            </b-form-group>
            <b-form-group label="Body">
              <b-form-textarea rows="4" v-model="model.body"></b-form-textarea>
            </b-form-group>
            <b-form-group label="image" v-slot="{ ariaDescribedby }">
              <b-form-radio v-model="model.imageUrl" :aria-describedby="ariaDescribedby" name="some-radios" value="https://picsum.photos/150/150/?image=1002"><b-img thumbnail fluid src="https://picsum.photos/150/150/?image=1002" alt="Image 1"></b-img></b-form-radio>
              <b-form-radio v-model="model.imageUrl" :aria-describedby="ariaDescribedby" name="some-radios" value="https://picsum.photos/150/150/?image=1025"><b-img thumbnail fluid src="https://picsum.photos/150/150/?image=1025" alt="Image 1"></b-img></b-form-radio>
              <b-form-radio v-model="model.imageUrl" :aria-describedby="ariaDescribedby" name="some-radios" value="https://picsum.photos/150/150/?image=103"><b-img thumbnail fluid src="https://picsum.photos/150/150/?image=103" alt="Image 1"></b-img></b-form-radio>
              <b-form-radio v-model="model.imageUrl" :aria-describedby="ariaDescribedby" name="some-radios" value="https://picsum.photos/150/150/?image=1042"><b-img thumbnail fluid src="https://picsum.photos/150/150/?image=1042" alt="Image 1"></b-img></b-form-radio>
            </b-form-group>
            <div>
              <b-btn type="submit" variant="success">Save Post</b-btn>
            </div>
          </form>
        </b-card>
        <b-card v-else>
          <h5 class="notAdminMsg">Only admin can create posts.</h5>
        </b-card>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import api from '@/api'
export default {
  data () {
    return {
      loading: false,
      posts: [],
      model: {},
      userName: ''
    }
  },
  async created () {
    this.refreshPosts()
    await this.refreshActiveUser()
  },
  watch: {
    // everytime a route is changed refresh the activeUser
    '$route': 'refreshActiveUser'
  },
  methods: {
    async refreshActiveUser () {
      if (this.authState.isAuthenticated) {
        let activeUser = await this.$auth.getUser()
        this.userName = activeUser.name
        console.log(this.userName)
      }
    },
    async refreshPosts () {
      this.loading = true
      this.posts = await api.getPosts()
      this.loading = false
      console.log(this.posts)
    },
    async populatePostToEdit (post) {
      this.model = Object.assign({}, post)
    },
    async savePost () {
      if (this.model.id) {
        await api.updatePost(this.model.id, this.model)
      } else {
        await api.createPost(this.model)
      }
      this.model = {} // reset form
      await this.refreshPosts()
    },
    async deletePost (id) {
      if (confirm('Are you sure you want to delete this post?')) {
        // if we are editing a post we deleted, remove it from the form
        if (this.model.id === id) {
          this.model = {}
        }
        await api.deletePost(id)
        await this.refreshPosts()
      }
    }
  }
}
</script>

<style>
  .post-card{
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
  }
</style>