<template>
  <main>
    <div class="comment-container" v-for="(c,i) in comments" :key="c.id">
      <Comment
        :commentsData="comments"
        :c="c"
        :index="i"
        @increase="increaseScore(i)"
        @decrease="decreaseScore(i, e)"
        @delete="deleteComment(i)"
        @add="newComment"
        @edited="updateData"
      />
      <div class="replies" v-if="c.replies.length">
        <Comment
          v-for="(r, e) in c.replies"
          :commentsData="comments"
          :key="r.id"
          :c="r"
          :index="i"
          :replyIndex="e"
          @increase="increaseScore(i, e)"
          @decrease="decreaseScore(i, e)"
          @delete="deleteComment(i, e)"
          @add="newComment"
          @edited="updateData"
        />
      </div>
    </div>
    <MainForm buttonText="Send" @add="newComment" />
  </main>
  <DeleteComment v-show="showConfirm" @cancel="showConfirm = false" />
</template>

<script>
import Comment from './components/mainComponent.vue'
import MainForm from './components/mainForm.vue'
import DeleteComment from './components/deleteComment.vue'

export default {
  data() {
    return {
      mainData: {},
      user: {},
      comments: [],
      showConfirm: false,
    }
  },
  methods: {
    increaseScore(i, e) {
      let comment = this.getComment(i, e)
      if (comment.userScoring == -1) {
        comment.userScoring = 1
        comment.score += 2
      } else if (comment.userScoring == 0) {
        comment.userScoring = 1
        comment.score++
      } else {
        comment.userScoring = 0
        comment.score--
      }
    },
    decreaseScore(i, e) {
      let comment = this.getComment(i, e)
      if (comment.userScoring == 1) {
        comment.userScoring = -1
        comment.score -= 2
      } else if (comment.userScoring == 0) {
        comment.userScoring = -1
        comment.score--
      } else {
        comment.userScoring = 0
        comment.score++
      }
    },
    deleteComment(i, e) {
      this.showConfirm = true
      let del = document.querySelector(".confirmDelete")
      del.onclick = () => {
        typeof e == "number" ? this.comments[i].replies.splice(e, 1) : this.comments.splice(i, 1)
        this.showConfirm = false
      }
    },
    getComment(i, e) {
      let c = {}
      typeof e == "number" ? c = this.comments[i].replies[e] : c = this.comments[i]
      this.comments.push("e")
      this.comments.pop()
      return c
    },
    newComment(text, i) {
      let comment = {
        "id": Date.now(),
        "content": text,
        "createdAt": "Now",
        "score": 0,
        "user": {
          "image": {
            "png": "/src/assets/images/avatars/image-juliusomo.png",
            "webp": "/src/assets/images/avatars/image-juliusomo.webp"
          },
          "username": "juliusomo"
        },
        "isUser": true,
        "userScoring": 0,
        "replies": []
      }
      if (typeof i == "number") {
        this.comments[i].replies.push(comment)
      } else {
        this.comments.push(comment)
      }
    },
    updateData(text, i, e) {
      if (typeof e == "number") {
        this.comments[i].replies[e].content = text
        this.comments.push("e")
        this.comments.pop()
      } else {
        this.comments[i].content = text
        this.comments.push("e")
        this.comments.pop()
      }
    }
  },
  async mounted() {
    if (localStorage.getItem("commentData")) {
      this.mainData = JSON.parse(localStorage.getItem("commentData"))
      this.user = this.mainData.currentUser
      this.comments = this.mainData.comments
    } else {
      let req = await fetch('/src/assets/data.json')
      let res = await req.json()
      this.mainData = res
      this.user = this.mainData.currentUser
      this.comments = this.mainData.comments
    }
  },
  updated() {
    localStorage.setItem("commentData", JSON.stringify(this.mainData))

    if (this.showConfirm) {
      document.body.classList.add("showConfirm")
    } else
      document.body.classList.remove("showConfirm")
  },
  components: { Comment, MainForm, DeleteComment },
}
</script>

<style>
:root {
  --Moderateblue: hsl(238, 40%, 52%);
  --SoftRed: hsl(358, 79%, 66%);
  --Lightgrayishblue: hsl(239, 57%, 85%);
  --Palered: hsl(357, 100%, 86%);
  --Darkblue: hsl(212, 24%, 26%);
  --GrayishBlue: hsl(211, 10%, 45%);
  --Lightgray: hsl(223, 19%, 93%);
  --Verylightgray: hsl(228, 33%, 97%);
  --White: hsl(0, 0%, 100%);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Rubik", sans-serif;
  background-color: var(--Verylightgray);
  display: flex;
  justify-content: center;
}

body.showConfirm {
  overflow-y: hidden;
}

body.showConfirm::before {
  content: "";
  position: fixed;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.233);
  z-index: 99;
}

.popUp {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  max-width: 340px;
  z-index: 100;
  background-color: white;
  padding: 20px 15px;
  border-radius: 10px;
  color: var(--Darkblue);
}

.popUp p {
  margin-block: 15px;
}

.popUp .choices {
  display: flex;
  gap: 10px;
}

.popUp span {
  flex-grow: 1;
  text-align: center;
  background-color: var(--SoftRed);
  color: white;
  font-weight: 500;
  padding-block: 10px;
  font-size: 15px;
  border-radius: 7px;
  cursor: pointer;
}

.popUp span:first-child {
  background-color: var(--GrayishBlue);
}

main {
  max-width: 833px;
  margin: 50px 10px;
}

.comment-container {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.comment {
  background-color: white;
  padding: 20px;
  border-radius: 6px;
  margin-bottom: 20px;
  display: flex;
  align-items: flex-start;
  gap: 15px;
  width: 100%;
}

.comment .container {
  flex-grow: 1;
}

.comment .avater {
  max-width: 31px;
}

.replies {
  width: 90%;
  position: relative;
  margin-bottom: 20px;
}

.replies .comment:last-child{
  margin-bottom: 0;
}

.replies::before {
  content: "";
  position: absolute;
  height: 100%;
  width: 3px;
  background-color: var(--Lightgray);
  left: -6%;
  border-radius: 3px;
}

.comment .info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.comment .info > div {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
  font-size: 14px;
}

.comment .info .name {
  font-weight: 500;
}

.comment .info .date {
  color: var(--GrayishBlue);
}

.comment .info .you {
  background-color: var(--Moderateblue);
  font-weight: 500;
  color: white;
  padding: 3px 7px;
}

.comment .content {
  color: var(--Darkblue);
}

.comment .mention {
  color: var(--Moderateblue);
  font-weight: 500;
}

.score {
  flex-basis: 40px;
  flex-grow: 0;
  flex-shrink: 0;
  aspect-ratio: 0.48;
  background-color: var(--Verylightgray);
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 5px;
  border-radius: 6px;
  flex-direction: column;
  color: var(--Moderateblue);
  font-weight: 500;
  user-select: none;
}

.score svg {
  cursor: pointer;
  fill: #c5c6ef;
}

.score svg.active {
  fill: var(--Moderateblue);
}

.comment .reply,
.comment .info .tools {
  color: var(--Moderateblue);
  font-weight: bold;
  cursor: pointer;
  font-size: 16px;
}

.comment .tools :first-child {
  color: var(--SoftRed);
  margin-right: 6px;
}

.new {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 20px;
  background-color: white;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 20px;
  width: 100%;
}

.new img {
  max-width: 40px;
}

.new textarea {
  flex-grow: 1;
  resize: none;
  aspect-ratio: 6.5;
  padding: 15px 20px;
  border: 2px solid var(--Lightgray);
  outline: none;
  border-radius: 8px;
  font-size: 16px;
}

.new .add {
  padding: 10px 16px;
  color: white;
  background-color: var(--Moderateblue);
  border-radius: 6px;
  text-transform: uppercase;
  font-size: 15px;
  user-select: none;
  cursor: pointer;
}

@media (max-width:570px) {
  .replies{
    width: 96%;
  }
  .replies::before{
    left: -15px;
  }
  .comment{
    flex-direction: column-reverse;
    padding: 15px;
    position: relative;
  }
  .score{
    flex-direction: row;
    aspect-ratio: initial;
    flex-basis: initial;
    padding: 7px 10px;
    gap: 10px;
  }
  .comment .reply , .comment .tools{
    position: absolute;
    right: 15px;
    bottom: 15px;
  }
  .new{
    padding:15px 10px 65px;
    position: relative;
  }
  .new textarea{ 
    aspect-ratio: initial;
    padding: 10px;
  }
  .new .add{
    position: absolute;
    right: 10px;
    bottom: 10px;
  }
  .new img{
    position: absolute;
    left: 10px;
    bottom: 10px;
  }
  .popUp{
    width: 340px;
    max-width: 90%;
  }
}
</style>
