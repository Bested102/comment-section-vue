<template>
  <div class="comment">
    <div class="score">
      <svg
        @click="$emit('increase')"
        ref="plus"
        width="11"
        height="11"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M6.33 10.896c.137 0 .255-.05.354-.149.1-.1.149-.217.149-.354V7.004h3.315c.136 0 .254-.05.354-.149.099-.1.148-.217.148-.354V5.272a.483.483 0 0 0-.148-.354.483.483 0 0 0-.354-.149H6.833V1.4a.483.483 0 0 0-.149-.354.483.483 0 0 0-.354-.149H4.915a.483.483 0 0 0-.354.149c-.1.1-.149.217-.149.354v3.37H1.08a.483.483 0 0 0-.354.15c-.1.099-.149.217-.149.353v1.23c0 .136.05.254.149.353.1.1.217.149.354.149h3.333v3.39c0 .136.05.254.15.353.098.1.216.149.353.149H6.33Z"
        />
      </svg>
      <span class="count">{{ c.score }}</span>
      <svg
        @click="$emit('decrease')"
        ref="minus"
        width="11"
        height="3"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M9.256 2.66c.204 0 .38-.056.53-.167.148-.11.222-.243.222-.396V.722c0-.152-.074-.284-.223-.395a.859.859 0 0 0-.53-.167H.76a.859.859 0 0 0-.53.167C.083.437.009.57.009.722v1.375c0 .153.074.285.223.396a.859.859 0 0 0 .53.167h8.495Z"
        />
      </svg>
    </div>
    <div class="container">
      <div class="info">
        <div>
          <img :src="avaterPath" alt class="avater" />
          <span class="name">{{ c.user.username }}</span>
          <span class="you" v-if="c.isUser">you</span>
          <span class="date">{{ c.createdAt }}</span>
        </div>
        <span class="reply" v-if="!c.isUser" @click="showForm = !showForm">
          <img src="../assets/images/icon-reply.svg" alt />
          Reply
        </span>
        <div class="tools" v-if="c.isUser">
          <span @click="$emit('delete')">
            <img src="../assets/images/icon-delete.svg" alt />
            Delete
          </span>
          <span @click="editComment">
            <img src="../assets/images/icon-edit.svg" alt />
            Edit
          </span>
        </div>
      </div>
      <p class="content" ref="content" @blur="sumbitComment" @keydown.enter="sumbitComment">
        <span v-if="c.replyingTo" class="mention">@{{ c.replyingTo }}</span>
        {{ c.content }}
      </p>
    </div>
  </div>
  <MainForm v-if="showForm" buttonText="Reply" @add="handleAdd" />
  
</template>

<script>
import MainForm from "./mainForm.vue";

export default {
  props: ["c", "commentsData", "index", "replyIndex"],
  data() {
    return {
      showForm: false
    }
  },
  methods: {
    handleAdd(text) {
      this.$emit("add", text, this.index)
      console.log(this.index)
      this.showForm = false
    },
    updateColors() {
      let btns = [this.$refs.plus, this.$refs.minus]
      if (this.c.userScoring === 1) {
        btns.forEach(e => e.classList.remove("active"))
        btns[0].classList.add("active")
      } else if (this.c.userScoring === -1) {
        btns.forEach(e => e.classList.remove("active"))
        btns[1].classList.add("active")
      } else {
        btns.forEach(e => e.classList.remove("active"))
      }
    },
    editComment() {
      this.$refs.content.setAttribute("contenteditable", true)
      this.$refs.content.focus()
    },
    sumbitComment() {
      this.$refs.content.setAttribute("contenteditable", false)
      this.$emit("edited", this.$refs.content.innerText, this.index, this.replyIndex)
    }
  },
  computed:{
    avaterPath(){
      return new URL(`/src/assets/images/${this.c.user.image.png}` ,import.meta.url).href
    }
  },  updated() {
    this.updateColors()
  },
  mounted() {
    this.updateColors()
  },
  emits: ['increase', 'decrease', 'delete', 'add', 'edited'],
  components: { MainForm}
}
</script>