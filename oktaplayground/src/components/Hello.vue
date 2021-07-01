<template>
  <div class="hero">
    <div>
      <h1 class="display-3">Hello {{this.userName}}</h1>
      <p class="lead">This is the homepage of your posts app.</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      msg: 'Welcome to Your Vue.js PWA',
      userName: ''
    }
  },
  async created () {
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
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.hero {
    height: 90vh;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
  }
  .hero .lead {
    font-weight: 200;
    font-size: 1.5rem;
  }
</style>
