<template>
  <header class="sticky z-20 bg-darkgray top-0 p-2 px-2">
    <nav class="flex justify-between">
      <div>
        <a href="/">
          <img
            class="w-48 md:w-60 inline-block"
            src="@/assets/img/logo-symb-nobg.svg"
          />
        </a>
      </div>

      <div class="hidden sm:block relative inline-block w-1/3">
        <SearchBar></SearchBar>
      </div>

      <div class="hidden sm:flex text-center text-purple-500">
        <div class="mr-5 text-3xl relative">
          <Notification></Notification>
        </div>
        <div class="mr-5 hover:text-purple-600 text-3xl">
          <button>
            <a href="/create-post"
              ><span class="material-icons"> queue </span></a
            >
          </button>
        </div>
        <div class="mr-5 hover:text-purple-600 text-3xl">
          <a href="/chat"
            ><span class="material-icons"> question_answer </span></a
          >
        </div>

        <div class="relative">
          <button
            v-on-clickaway="away"
            v-on:click="show = !show"
            class="
              flex
              items-center
              outline-none
              focus:outline-none
              rounded-full
            "
            :class="{ 'focus:ring-2 focus:ring-purple-500': show === true }"
          >
            <img class="h-8 w-8 rounded-full" :src="`https://cdn.hokusai.codes/profile/${this.$store.getters.UserId}.jpg?${cacheStr}`" @error="$event.target.src = 'https://cdn.hokusai.codes/profile/default.jpg'"/>
          </button>
          <!-- Dropdown Body -->
          <transition
            mode="out-in"
            enter-active-class="animate__animated animate__fadeInDown"
            leave-active-class="animate__animated animate__fadeOutUp"
          >
            <div
              v-if="show"
              id="dropBodyMenu"
              class="
                z-0
                absolute
                right-0
                w-40
                mt-2
                py-2
                bg-lightgray
                border border-lightgray
                rounded-lg
                text-white
              "
            >
              <ul class="text-left">
                <a :href="`/${this.username}`">
                  <li class="my-2 hover:text-gray-300 cursor-pointer px-4">
                    Perfil 
                  </li>
                </a>
                <a :href="`/statistics`">
                  <li class="my-2 hover:text-gray-300 cursor-pointer px-4">
                    Estatísticas 
                  </li>
                </a>
                <li
                  class="my-2 hover:text-gray-300 cursor-pointer"
                >
                  <a class="px-4 py-2" @click="logout"> Logout </a>
                </li>
              </ul>
            </div>
          </transition>
          <!-- // Dropdown Body -->
        </div>
      </div>
    </nav>
  </header>
</template>

<script>
import Notification from "./Notification";
import { directive as onClickaway } from "vue-clickaway";
import SearchBar from "./SearchBar.vue";
import { mapActions } from "vuex";

export default {
  name: "Header",
  components: {
    Notification,
    SearchBar,
  },
  directives: {
    onClickaway: onClickaway,
  },
  computed: {
    openModal() {
      this.modalState = true;
    },
  },
  data() {
    return {
      show: false,
      showModal: false,
      username: this.$store.getters.Username,
      userId: this.$store.getters.UserId,
      cacheStr: Math.random().toString(36).substring(7)
    };
  },
  methods: {
    ...mapActions(["LogOut"]),
    away: function () {
      this.show = false;
    },
    awayModalCreatePost: function () {
      this.showModal = false;
    },
    logout: async function () {
      await this.$store.dispatch("LogOut");
      this.$router.push("/login");
    },
  },
};
</script>

<style scoped>
</style>
<style>
.animate__animated.animate__fadeIn {
  --animate-duration: 0.3s;
}
.animate__animated.animate__fadeOut {
  --animate-duration: 0.3s;
}
.vs__selected {
  background-color: #3b3b3b;
  color: white;
}
.vs__deselect {
  fill: #8b5cf6;
}
.vs__deselect:hover {
  fill: #7c3aed;
}
.vs__dropdown-menu {
  background-color: #3b3b3b;
}
.vs__actions {
  cursor: pointer;
}
.vs__dropdown-option {
  color: white;
}
.vs__dropdown-option--highlight {
  background-color: #1e1e1e;
}
.vs__open-indicator {
  fill: #8b5cf6;
}
</style>