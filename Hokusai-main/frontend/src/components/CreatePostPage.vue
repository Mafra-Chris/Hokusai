<template>
  <div>
    <Header></Header>
    <main class="w-full mt-4">
      <div
        class="
          h-full
          md:max-w-4xl
          mx-auto
          bg-lightgray
          rounded-lg
          flex
          content-center
          p-5
        "
      >
        <transition
          mode="out-in"
          enter-active-class="animate__animated animate__fadeIn"
          leave-active-class="animate__animated animate__fadeOut"
        >
          <div v-if="postType == 2" class="mx-auto my-auto text-center">
            <h1 class="text-white font-bold text-xl mb-5">
              Comece a criar uma nova postagem:
            </h1>
            <div class="text-purple-500">
              <button class="focus:outline-none" @click="choosePostType(0)">
                <span
                  class="
                    material-icons
                    p-6
                    bg-purple-500 bg-opacity-10
                    hover:bg-opacity-100
                    hover:text-white
                    rounded-full
                    m-3
                    mb-1
                    transition
                    duration-200
                    ease-in-out
                  "
                  >collections</span
                >
                <h2 class="text-white font-medium cursor-default">Imagens</h2>
              </button>
              <button class="focus:outline-none" @click="choosePostType(1)">
                <span
                  class="
                    material-icons
                    p-6
                    bg-purple-500 bg-opacity-10
                    hover:bg-opacity-100
                    hover:text-white
                    rounded-full
                    m-3
                    mb-1
                    transition
                    duration-200
                    ease-in-out
                  "
                  >format_size</span
                >
                <h2 class="text-white font-medium cursor-default">Texto</h2>
              </button>
            </div>
          </div>
        </transition>

        <transition
          mode="out-in"
          enter-active-class="animate__animated animate__fadeIn"
          leave-active-class="animate__animated animate__fadeOut"
        >
          <div
            v-if="postType == 0"
            class="mx-auto w-full flex flex-col items-center w-full md:w-4/6"
          >
            <button
              class="focus:outline-none self-start mb-6"
              @click="choosePostType(2)"
            >
              <span
                class="
                  material-icons
                  text-purple-500
                  hover:text-purple-600
                  text-3xl
                "
              >
                arrow_back
              </span>
            </button>

            <h1 class="text-white font-bold text-xl mb-5 self-start">
              Nova Postagem
            </h1>

            <form @submit.prevent="publishImage" class="w-full">
              <vue-dropzone
                ref="myVueDropzone"
                id="dropzone"
                :options="dropzoneOptions"
                class="mb-5"
                @vdropzone-complete="afterComplete"
              ></vue-dropzone>
              <label class="text-white font-medium" for="description"
                >Descrição</label
              >
              <textarea
                class="
                  bg-lightergray
                  rounded-lg
                  w-full
                  focus:outline-none
                  p-2
                  text-white
                "
                name="description"
                maxlength="1000"
              ></textarea>
              <label class="text-white font-medium" for="tags">Tags</label>
              <v-select
                taggable
                multiple
                push-tags
                name="tags"
                label="name"
                @option:created="addTag"
                @input="appendTag"
                :options="recomendedTags"
                :selectable="() => selectedTags.length < 5"
              ></v-select>
              <button
                class="
                  rounded-lg
                  bg-purple-500
                  hover:bg-purple-600
                  focus:outline-none
                  text-white
                  p-2
                  mx-1
                  mt-2
                "
              >
                Publicar
              </button>
            </form>
          </div>
        </transition>
        <transition
          mode="out-in"
          enter-active-class="animate__animated animate__fadeIn"
          leave-active-class="animate__animated animate__fadeOut"
        >
          <div
            v-if="postType == 1"
            class="mx-auto w-full flex flex-col items-center w-full md:w-4/6"
          >
            <button
              class="focus:outline-none self-start mb-6"
              @click="choosePostType(2)"
            >
              <span
                class="
                  material-icons
                  text-purple-500
                  hover:text-purple-600
                  text-3xl
                "
              >
                arrow_back
              </span>
            </button>

            <h1 class="text-white font-bold text-xl mb-5 self-start">
              Nova Postagem
            </h1>
            <ckeditor
              :editor="editor"
              v-model="editorData"
              :config="editorConfig"
            ></ckeditor>
            <form @submit.prevent="publishText" class="w-full">
              <label class="text-white font-medium mt-5" for="description"
                >Descrição</label
              >
              <textarea
                class="
                  bg-lightergray
                  rounded-lg
                  w-full
                  focus:outline-none
                  p-2
                  text-white
                "
                name="description"
                maxlength="1000"
              ></textarea>
              <label class="text-white font-medium mt-5" for="tags">Tags</label>
              <v-select
                taggable
                multiple
                push-tags
                name="tags"
                label="name"
                @option:created="addTag"
                @input="appendTag"
                :options="recomendedTags"
                :selectable="() => selectedTags.length < 5"
              ></v-select>
              <button
                class="
                  rounded-lg
                  bg-purple-500
                  hover:bg-purple-600
                  focus:outline-none
                  text-white
                  p-2
                  mx-1
                  mt-4
                  self-start
                "
              >
                Publicar
              </button>
            </form>
          </div>
        </transition>
      </div>
    </main>
    <Footer></Footer>
  </div>
</template>

<script>
import Header from "./Header.vue";
import Footer from "./Footer.vue";
import vue2Dropzone from "vue2-dropzone";
import "../assets/css/dropzone.css";
import axios from "axios";
import ClassicEditor from "@ckeditor/ckeditor5-editor-classic/src/classiceditor";
import "vue-select/dist/vue-select.css";
import EssentialsPlugin from "@ckeditor/ckeditor5-essentials/src/essentials";
import BoldPlugin from "@ckeditor/ckeditor5-basic-styles/src/bold";
import ItalicPlugin from "@ckeditor/ckeditor5-basic-styles/src/italic";
import AlignmentPlugin from "@ckeditor/ckeditor5-alignment/src/alignment";
import ParagraphPlugin from "@ckeditor/ckeditor5-paragraph/src/paragraph";
import HeadingPlugin from "@ckeditor/ckeditor5-heading/src/heading";
import BlockQuotePlugin from "@ckeditor/ckeditor5-block-quote/src/blockquote";
import vSelect from "vue-select";
export default {
  name: "CreatePostPage",
  components: {
    Header,
    Footer,
    vSelect,
    vueDropzone: vue2Dropzone,
  },
  data: function () {
    return {
      dropzoneOptions: {
        url: "https://httpbin.org/post",
        thumbnailWidth: 140,
        maxFilesize: 5,
        headers: { "My-Awesome-Header": "header value" },
        dictDefaultMessage:
          "<span class='material-icons text-purple-500 text-4xl'>cloud_upload</span>",
        addRemoveLinks: true,
        dictFileTooBig:
          "Arquivo muito grande. Tamanho máximo {{maxFilesize}}MB",
        dictInvalidFileType: "Arquivo inválido",
        dictRemoveFile: "<span class='material-icons'>close</span>",
        dictMaxFilesExceeded:
          "Número máximo de arquivos permitidos: {{maxFiles}}",
        maxFiles: 10,
      },
      postType: 2,
      recomendedTags: [],
      selectedTags: [],
      imageArray: [],
      editor: ClassicEditor,
      editorData: "<p>Digite seu texto aqui.</p>",
      editorConfig: {
        plugins: [
          EssentialsPlugin,
          BoldPlugin,
          ItalicPlugin,
          ParagraphPlugin,
          AlignmentPlugin,
          HeadingPlugin,
          BlockQuotePlugin,
        ],

        toolbar: {
          items: [
            "heading",
            "|",
            "bold",
            "italic",
            "|",
            "alignment",
            "blockQuote",
            "|",
            "undo",
            "redo",
          ],
        },
      },
    };
  },
  mounted() {
    axios
      .get("/tags/all")
      .then((response) => (this.recomendedTags = response["data"]));
  },
  methods: {
    choosePostType: function (type) {
      this.postType = null;
      setTimeout(() => {
        this.postType = type;
      }, 200);
    },
    afterComplete(file) {
      if (file.status === "success") this.imageArray.push(file.dataURL);
    },
    publishImage: function (e) {
      if (this.imageArray.length > 0) {
        axios
          .post(
            "/post/new",
            JSON.stringify({
              description: e.target.elements.description.value,
              postType: this.postType,
              slides: this.imageArray,
              tags: this.selectedTags,
            })
          )
          .then((data) => {
            var vm = this
            setTimeout(function(){ 
              window.location = `/${vm.$store.getters.Username}?post=${data['data']['post_id']}`;
            }, 500);
          });
      }
    },
    publishText: function (e) {
      axios
        .post(
          "/post/new",
          JSON.stringify({
            body: this.editorData,
            description: e.target.elements.description.value,
            postType: this.postType,
            tags: this.selectedTags,
          })
        )
        .then((data) => {
          var vm = this
          setTimeout(function(){ 
              window.location = `/${vm.$store.getters.Username}?post=${data['data']['post_id']}`;
          }, 500);
        });
    },
    addTag: async function (e) {
      await axios
        .post("/tag/new", { name: e["name"] })
        .then((res) => (e["tag_id"] = res["data"]["id"]));
    },
    appendTag(e) {
      let lastTag = e[e.length - 1];

      let duplicatedTag = this.recomendedTags.find(
        (tag) => tag.name == lastTag.name
      );

      if (duplicatedTag) {
        this.selectedTags.push(duplicatedTag);
      } else {
        this.selectedTags.push(lastTag);
      }

    },
  },
};
</script>

<style scoped>
main {
  height: 90vh;
}
.vue-dropzone {
  color: white;
  background: none;
  border-radius: 0.5rem;
  max-height: 24rem;
  border-style: dashed;
  border-color: #656565;
  overflow: auto;
  padding: 1rem;
}

.vue-dropzone::-webkit-scrollbar {
  width: 10px; /* width of the entire scrollbar */
}
.vue-dropzone::-webkit-scrollbar-track {
  background: none; /* color of the tracking area */
}
.vue-dropzone::-webkit-scrollbar-thumb {
  background-color: #3b3b3b;
  border-radius: 0.5rem;
}

.animate__animated {
  --animate-duration: 0.1s;
}
</style>
<style>
.ck.ck-editor {
  width: 100%;
  max-width: 100%;
  max-height: 85%;
  overflow: auto;
  height: 60%;
}
.ck.ck-editor__main {
  height: 100%;
}
.ck.ck-content.ck-editor__editable.ck-rounded-corners.ck-editor__editable_inline {
  height: 100%;
}
</style>