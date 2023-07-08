<template>
  <div>
    <h1>Страница с постами</h1>
    <my-input v-focus :model-value="searchQuery" @update:model-value="setSearchQuery" placeholder="Поиск..."/>
    <div class="app__btns">
      <my-button @click="showDialog" style="">Создать пост</my-button>
      <my-select :model-value="selectedSort" @update:model-value="setSelectedSort" :options="sortOptions"/>
    </div>

    <my-dialog v-model:show="dialogVisible" >
      <post-form @create="createPost"/>
    </my-dialog>
    <post-list v-if="!isPostLoading" :posts="selectedAndSearchedPosts" @remove="removePost"/>
    <div v-else>Идет загрузка...</div>
    <div v-intersection="loadMorePosts" class="observer"></div>
        <div class="page__wrapper">
          <div class="page" :class="{'current-page':page === pageNumber}" @click="changePage(pageNumber)" v-for="pageNumber in totalPage" :key = "pageNumber">{{pageNumber}}</div>
        </div>
  </div>
  <strong></strong>
</template>

<script>
import PostList from "@/components/PostList";
import PostForm from "@/components/PostForm";
import axios from 'axios'
import {mapState, mapGetters, mapMutations, mapActions} from 'vuex'
export default {
  components:{
    PostForm, PostList
  },
  name: "App",
  data(){
    return{
      dialogVisible: false,
      modificatorValue: '',
    }
  },
  methods:{
    ...mapMutations({
      setPage: 'post/setPage',
      setSearchQuery : 'post/setSearchQuery',
      setSelectedSort: 'post/setSelectedSort'
    }),
    ...mapActions({
      loadMorePosts: 'post/loadMorePosts',
      fetchPosts: 'post/fetchPosts'
    }),
    createPost(post){
      this.posts.push(post)
      this.dialogVisible = false
    },
    removePost(post){
      this.posts = this.posts.filter(p=>p.id !==  post.id)
    },
    showDialog(){
      this.dialogVisible = true
    },

  },
  mounted() {
    this.fetchPosts()
  },
  computed:{
    ...mapState({
      posts: state => state.post.posts,
      isPostLoading :state => state.post.isPostLoading,
      selectedSort: state => state.post.selectedSort,
      searchQuery: state => state.post.searchQuery,
      page: state => state.post.page,
      limit:state => state.post.limit,
      totalPage: state => state.post.totalPage,
      sortOptions: state => state.post.sortOptions
    }),
    ...mapGetters({
      sortedPosts: 'post/sortedPosts' ,
      selectedAndSearchedPosts:'post/selectedAndSearchedPosts'
    })
  },
  watch:{
    // page(){
    //   this.fetchPosts()
    // }
  }
}
</script>

<style>

.app__btns{
  display: flex;
  justify-content: space-between;
  margin: 15px 0
}
.page__wrapper{
  display: flex;
  margin-top: 15px;
}
.page{
  border: 1px solid black;
  padding: 10px;
}
.current-page{
  border: 2px solid teal;
}
.observer{
  height: 30px;
  background: green;
}
</style>