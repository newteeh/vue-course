<template>
  <div>
    <h1>Страница с постами</h1>
    <my-input v-focus v-model="searchQuery" placeholder="Поиск..."/>
    <div class="app__btns">
      <my-button @click="showDialog" style="">Создать пост</my-button>
      <my-select v-model="selectedSort" :options="sortOptions"/>
    </div>

    <my-dialog v-model:show="dialogVisible" >
      <post-form @create="createPost"/>
    </my-dialog>
    <post-list v-if="!isPostLoading" :posts="selectedAndSearchedPosts" @remove="removePost"/>
    <div v-else>Идет загрузка...</div>
    <div v-intersection="loadMorePosts" class="observer"></div>
    <!--    <div class="page__wrapper">-->
    <!--      <div class="page" :class="{'current-page':page === pageNumber}" @click="changePage(pageNumber)" v-for="pageNumber in totalPage" :key = "pageNumber">{{pageNumber}}</div>-->
    <!--    </div>-->
  </div>
  <strong></strong>
</template>

<script>
import PostList from "@/components/PostList";
import PostForm from "@/components/PostForm";
import axios from 'axios'
export default {
  components:{
    PostForm, PostList
  },
  name: "App",
  data(){
    return{
      posts:[

      ],
      dialogVisible: false,
      modificatorValue: '',
      isPostLoading :false,
      selectedSort:'',
      searchQuery:'',
      page: 1,
      limit:10,
      totalPage: 0,
      sortOptions:[
        {value:'title', name:'По названию'},
        {value:'body', name:'По содержанию'},
      ]
    }
  },
  methods:{
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
    // changePage(pageNumber){
    //   this.page = pageNumber
    // },
    async fetchPosts(){
      try {
        this.isPostLoading = true
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts',{
          params:{
            _page:this.page,
            _limit:this.limit
          }
        });
        this.totalPage = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.posts = response.data
        this.isPostLoading = false

      } catch (e){
        alert('Ошибка')
      } finally {

      }
    },
    async loadMorePosts(){
      try {
        this.page += 1

        const response = await axios.get('https://jsonplaceholder.typicode.com/posts',{
          params:{
            _page:this.page,
            _limit:this.limit
          }
        });
        this.totalPage = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.posts = [...this.posts, ...response.data]
        this.isPostLoading = false

      } catch (e){
        alert('Ошибка')
      }
    }
  },
  mounted() {
    this.fetchPosts();
    // console.log(this.$refs.observer);
    // const options = {
    //   rootMargin: '0px',
    //   threshold: 1.0
    // }
    // const callback = (entries, observer) => {
    //   if (entries[0].isIntersecting && this.page < this.totalPage){
    //     this.loadMorePosts()
    //   }
    // };
    // const observer = new IntersectionObserver(callback, options);
    // observer.observe(this.$refs.observer)

  },
  computed:{
    sortedPosts(){
      return [...this.posts].sort((post1,post2)=> post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])
      )
    },
    selectedAndSearchedPosts(){
      return this.sortedPosts.filter(post=>post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    }
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