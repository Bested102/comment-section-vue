<template>
  <div class="new">
    <img src="../assets/images/image-juliusomo.png"/>
    <textarea v-model="newComment" placeholder="Add a comment..."></textarea>
    <span class="add"  @click="submit">{{buttonText}}</span>
  </div>
</template>

<script>
  export default{
    data(){
      return{
        newComment:""
      }
    },
    methods:{
      submit(){
      if (this.newComment) this.$emit('add' , this.newComment)
      this.newComment = ""
      }
    },
    props:["buttonText"],
    emits:["add"]
  }
</script>
