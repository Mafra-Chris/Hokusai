<template>

  <div
    class="bg-lightgray border border-lightgray rounded-lg block w-full text-white"
  >
    <div class="flex items-center px-4 py-3">
      <a class="flex items-center" :href="'/'+postData.username">
      <img class="h-8 w-8 rounded-full" :src="`https://cdn.hokusai.codes/profile/${postData.author_id}.jpg?${cacheStr}`"  @error="$event.target.src = 'https://cdn.hokusai.codes/profile/default.jpg'" />
      <div class="ml-3">
        <span class="text-sm font-semibold antialiased block leading-tight">{{
          postData.username
        }}</span>
      </div>
      </a>
    </div>

    <div class="carousel-div bg-black bg-opacity-50">
      <carousel
        v-if="postData.postType == 0"
        :per-page="1"
        :mouse-drag="true"
        :centerMode="true"
        :paginationEnabled="false"
        :navigationEnabled="true"
        v-on:pageChange="pageChange"
        navigationNextLabel="<span class='material-icons text-white bg-black bg-opacity-70 rounded-md'>
chevron_right
</span>"
        navigationPrevLabel="<span class='material-icons text-white bg-black bg-opacity-70 rounded-md'>
chevron_left
</span>"
      >
        <slide
          v-for="(slide, index) in postData.slides"
          :key="index"
          class="m-auto float-right"
        >
          <img :src="`https://cdn.hokusai.codes/posts/${slide}`"  @error="$event.target.src = 'https://cdn.hokusai.codes/posts/default.jpg'" class="object-contain mx-auto my-auto max-h-70vh"/>
        </slide>
      </carousel>
      <div
        class="w-100 text-left p-3 overflow-y-auto"
        style="height: 60vh"
        v-if="postData.postType == 1"
        v-html="postData.body"
      >

      </div>
    </div>

    <div class="flex items-center justify-between mx-3 mt-1 text-purple-500">
      <div class="relative flex -ml-2">
        <button class="focus:outline-none">
          <span
            class="material-icons text-gray-500 hover:text-purple-500"
            :class="vote == 0 ? 'text-purple-500' : ''"
            @click="chooseVote(0)"
          >
            arrow_upward
          </span>
        </button>
        <span class="text-white mx-1">{{postData.likes}}</span>
        <button class="focus:outline-none">
          <span
            class="material-icons text-gray-500 hover:text-red-600"
            :class="vote == 1 ? 'text-red-600' : ''"
            @click="chooseVote(1)"
          >
            arrow_downward
          </span>
        </button>
      </div>
      <div class="relative flex">
        <span
          v-if="postData.postType == 0"
          class="bg-black p-1 px-2 rounded-lg text-sm font-semibold bg-opacity-50 text-white"
          >{{ currentPage + 1 }}/{{ postData.slides.length }}</span
        >
      </div>
      
    </div>

    <div class="mx-4 mb-4 text-left">
      <span class="text-sm font-semibold antialiased leading-tight"
        >{{ postData.username }}
      </span>
      <span
        ><span class="text-sm antialiased leading-tight">
          {{ postData.description }}
        </span>
      </span
      >
    </div>
  </div>
</template>

<script>
import { directive as onClickaway } from "vue-clickaway";
import axios from "axios";
export default {
  name: "Post",
  props: ["postData"],

  directives: {
    onClickaway: onClickaway,
  },
  data: () => ({
    isActive: true,
    isDisabled: false,
    vote: null,
    bookmarkType: "bookmark_border",
    currentPage: 0,
    cacheStr: Math.random().toString(36).substring(7)
  }),
  mounted(){
    if(this.postData.like)
      this.vote = 0
    else if (this.postData.dislike)
      this.vote = 1
  },
  methods: {
    pageChange(i) {
      this.currentPage = i;
    },

    chooseVote: function(voteType){
      this.vote = voteType
      if (voteType == 0){
        axios.post('/post/like', {post_id: this.postData.post_id})
        .then(res => {
          this.postData.likes = res['data'];
          this.vote = 0
        });
      } else {
        axios.post('/post/dislike', {post_id: this.postData.post_id})
        .then(res => {
          this.postData.likes = res['data'];
          this.vote = 1
        });
      }
    }
  },
};
</script>

<style>
button.VueCarousel-dot:focus {
  outline: none;
}

.carousel-div.VueCarousel {
  max-height: 100%;
}
button.VueCarousel-navigation-button{
  display: none;
}

@media (min-width: 1024px) { 
  button.VueCarousel-navigation-button{
  display: inline-block;
}
}
button.VueCarousel-navigation-button.VueCarousel-navigation-next {
  right: 2.5rem;
  
}

button.VueCarousel-navigation-button.VueCarousel-navigation-prev {
  left: 2.5rem;
  
}

button.VueCarousel-navigation-button.VueCarousel-navigation-next:focus, button.VueCarousel-navigation-button.VueCarousel-navigation-prev:focus{
  outline: none;
}


</style>
