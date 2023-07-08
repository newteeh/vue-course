<template>
  <div v-if="posts.length > 0">
    <div>
      <h3>Список постов</h3>
      <transition-group name="post-list">
        <post-item  v-for="post in posts" :post="post" :key="post.id" @remove="$emit('remove', post)"/>
      </transition-group>
    </div>

  </div>
  <h2 style="color: red" v-else>
    Список постов пуст
  </h2>

</template>

<script>
import PostItem from "@/components/PostItem";
export default {
  name: "PostList",
  components: {PostItem},
  props:{
    posts:{
      type:Array,
      required:true
    }
  }
}
</script>

<style scoped>
.post-list-item{
    display: inline-block;
    margin-right: 10px ;
  }
.post-list-enter-active,
.post-list-leave-active{
  transition: all 0.4s ease;
}
.post-list-enter-from,
.post-list-leave-to{
  opacity: 0;
  transform: translateX(130px);
}
.post-list-move{
  transition: transform 0.8s ease;
}
</style>