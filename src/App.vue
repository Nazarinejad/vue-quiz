<template>
  <div id="app">
    <div class="container">

      <div class="search-bar">
        <div class="search-box">
          <input 
            type="text"
            v-model="postInput"
            placeholder="Search something..."
            @keyup="search" />
        </div>
        <button @click="addItem">
          <img src="@/assets/images/plus.svg" />
        </button>
      </div>

      <ul>
        <li v-for="filteredItem of filteredPosts" :key="filteredItem.id">
          <div class="seperator">
            <img class="post-img" :src="filteredItem.image" alt="">
            <div class="text-wrapper">
              <h4>
                {{filteredItem.title}}
              </h4>
              <p>
              {{filteredItem.description}}
              </p>
            </div>
            <button type="button" @click="deleteItem(filteredItem.id)">
              <img class="delete-icon" src="@/assets/images/trash-can.svg">
            </button>
          </div>
          <div class="progress-bar"></div>
        </li>
      </ul>

    </div>
  </div>
</template>

<script>
import axios from 'axios';
const baseURL = "http://localhost:3000/todos/"

export default {
  name: 'App',
  data() {
    return {
      allPosts: [],
      filteredPosts:[],
      postInput: ''
    }
  },
  async created() {
    try {
      const res = await axios.get(baseURL);
      this.allPosts = res.data;
      this.filteredPosts = res.data;
    } catch(e) {
      console.error(e)
    }
  },
  methods: {
    async search(){
      await axios.get(baseURL)
        .then(response => {
          const res = response.data;
          this.filteredPosts = res.filter(allPosts => {return allPosts.title.includes(this.postInput) || allPosts.description.includes(this.postInput)})
        })
    },
    async addItem(){
      const res = await axios.post(baseURL, { title: this.postInput, description: this.lastPost.description, image: this.lastPost.image })
      this.allPosts = [...this.allPosts, res.data]
      this.postInput = ''
    },
    async deleteItem(id){
       const res = await axios.delete(baseURL + id);
       this.allPosts = [...this.allPosts, res.data];
    }
  },
  computed:{
    lastPost(){
      if (this.allPosts.length == 0) return;
      return this.allPosts.reduce((a,b) => Number(a.id) > Number(b.id) ? a : b);    
    }
  },
}
</script>

<style scoped lang="scss">

$search-bar-color:#dce2e8;
$search-box-color:#f0f0f0;
$search-icon: 'assets/images/loupe.svg';
$plus-icon: 'assets/images/plus.svg';
$title-color:#6e6e6e;
$description-color:#979797;

#app {
  display:flex;
  justify-content: center;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding-top: 2%;
  text-align: center;

  .container{
    align-items: center;
    display:flex;
    flex-direction: row;
    flex-wrap: wrap;
    width:100%;
    max-width: 500px;
    padding: 1rem;


    .search-bar{
      background-color: $search-bar-color;
      display:flex;
      flex-direction: row-reverse;
      width:100%;
      padding: 10px 15px;
      background-color: #dce2e8;
      border-radius:10px;
      .search-box{
        border-radius: 25px;
        background-color: $search-box-color;
        position: relative;
        flex-grow: 1;
        padding: 5px;
        &:before{
          display:block;
          position: absolute;
          left: 15px;
          width:24px;
          top: 8px;
          height:24px;
          background-image: url($search-icon);
          background-size: contain;
          content:"";
        }
        input{
          position: relative;
          width:100%;
          height: 32px;
          border:0;
          padding-left: 45px;
          z-index:2;
          background-color: transparent;
          box-shadow: none;

          &:hover,&:active,&:focus{
            border:0 !important;
            box-shadow: none;
          }
        }
      }
      button{
        z-index:1;
        height: 42px;
        width: 42px;
        margin-right: 15px;
        padding: 10px;
        border-radius: 100%;
        border:0;
        background-color: $search-box-color;
        cursor:pointer;
        img{
          width:20px;
        }
      }
    }
    ul{
      display: flex;
      flex-flow: column;
      padding: 0;
      width: 100%;
      li {
        width: 100%;
        list-style: none;
        display: flex;
        flex-direction: column;
        border-radius: 5px;
        padding: 15px;
        margin:10px 0;
        &:nth-child(3n-2){
          background-color: #ece9f3;
        }
        &:nth-child(3n-1){
          background-color: #eaf0f4;
        }
        &:nth-child(3n){
          background-color: #eaf4f0;
        }
        .seperator{
          display: flex;
          flex-direction: row-reverse;
          margin-bottom: 15px;
          img.post-img{
            align-self: flex-end;
            width:60px;
            height: 60px;
            border-radius: 10px;
          }
          .text-wrapper{
            align-self: flex-end;
            text-align: right;
            flex-grow: 1;
            padding: 0 15px;
            h4{
              color:$title-color;
              margin-bottom: 5px;
              font-size: 0.9rem;
              word-spacing: -1px;
            }
            p{
              color: $description-color;
              margin: 0;
              font-size: 0.7rem;
              word-spacing: -1px;
            }
          }
          button{
            max-width: 40px;
            margin: 0;
            padding: 0;
            line-height: 0;
            border:0;
            align-self: flex-end;
            cursor: pointer;
            background-color: transparent;
            img.delete-icon{
              width: 100%;
              height: 26px;
            }
          }
        }
        
        .progress-bar{
          width:100%;
          height: 8px;
          border-radius: 10px;
        }
        &:nth-child(3n-2) .progress-bar{
          background-color: #b148f1;
        }
        &:nth-child(3n-1) .progress-bar{
          background-color: #3fbaff;
        }
        &:nth-child(3n) .progress-bar{
          background-color: #3fffdc;
        }
        
      }
    }
    
  }

}


</style>
