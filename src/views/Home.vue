<template>
  <v-app>
    <v-app-bar dark app class="px-2">
      <v-app-bar-nav-icon @click="isOpen = true"><v-icon>menu</v-icon></v-app-bar-nav-icon>
      <v-toolbar-title class="pl-1">Categories</v-toolbar-title>
    </v-app-bar>
    <v-navigation-drawer
      v-model="isOpen"
      fixed
      temporary
    >
      <v-list
        nav
        dense
      >
        <v-list-item-group
          v-model="category"
          active-class="orange--text text--accent-4"
        >
          <v-list-item @click="isOpen = false">
            <v-list-item-title>All</v-list-item-title>
          </v-list-item>

          <v-list-item v-for="(tab, i) of categories" :key="i" @click="isOpen = false">
            <v-list-item-title>— {{tab}}</v-list-item-title>
          </v-list-item>

        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>
    <v-container class="d-flex card-wrapper flex-wrap">
      <v-row v-if="getFilteredCourses" justify="center" justify-lg="start">
        <v-col
          v-for="(course, i) of getFilteredCourses"
          cols="auto"
          lg="6"
          xl="3"
          :key="i"
        >
          <v-card
            class="my-2 mx-lg-auto mx-xl-0"
            max-width="400"
          >
            <v-img
              class="white--text align-end"
              height="200px"
              :src="getImage(course.image.url)"
            >
                <v-card-title>{{course.title}}</v-card-title>
            </v-img>

            <v-card-text class="text--primary">
              {{getText(course.description)}}
            </v-card-text>

            <v-card-subtitle class="pb-2 pt-0 justify-space-between d-flex align-center">
              <div class="d-flex align-center">
                <v-icon :color="course.price ? 'inherit' : 'green'">attach_money</v-icon>
                <span class="mr-2" v-if="course.price">{{course.price}}</span>
                <b class="mr-2 green--text text-uppercase" v-else>Free</b>
                <v-icon class="mr-1">schedule</v-icon>
                <span class="mr-2">{{course.duration}} min</span>
              </div>
              <v-btn color="orange" text>more info</v-btn>
            </v-card-subtitle>
          </v-card>
        </v-col>
      </v-row>
      <div class="title" v-else>There's no courses yet...</div>
    </v-container>
  </v-app>  
</template>

<script>
import axios from 'axios';

export default {
  name: "App",

  data: () => ({
    isOpen: false,
    categories: [],
    courses: [],
    category: 0
  }),
  mounted() {
    axios.get('http://localhost:1337/courses').then(response => {
      this.courses = response.data
    });
    axios.get('http://localhost:1337/categories').then(response => {
      this.categories = response.data.map(el => el.name)
    })
  },
  computed: {
    getFilteredCourses() {
      if (!this.category) {return this.courses}
      return this.courses.filter(el => el.categories[0].name === this.categories[this.category - 1])
    }
  },
  methods: {
    getText(text) {
      if (text.length > 210) {
        return text.slice(0, 210) + '...'
      }
      return text
    },
    getImage(url) {
      return `http://localhost:1337${url}`
    }
  }
};
</script>
<style lang="scss" scoped>
  .card-wrapper {
    padding-top: 100px;
  }
  .card-item {
    margin: 0 auto;
  }
</style>