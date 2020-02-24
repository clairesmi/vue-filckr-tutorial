<template>
  <div class="home">
    <div class="title-container">
      <h1 class="title">LION AND GIRAFFE FINDER</h1>
    </div>
      <div class="header-info">
    <form>
      <label>
        <p class="question-text">Type <strong>Lions</strong> or <strong>Giraffes</strong></p>
        <input v-model="tag" type="text">
      </label>
      <button type="submit" class="search-button" @click.prevent="search">Search</button>
    </form>
      <div class="select-container">
        <div class="current-page-label">
          <p>Page {{this.page}}</p>
        </div>
        <select v-model="per_page" @input="handleSelect">
          <option name="per_page" value="25">25 per page</option>
          <option name="per_page" value="50">50 per page</option>
          <option name="per_page" value="75">75 per page</option>
          <option name="per_page" value="100">100 per page</option>
        </select>
      </div>
    </div>
      <ul v-if="images" class="image-list">
        <image-card v-for="image in images" :key="image.id" :image="image"/>
      </ul>
  <div v-if="searchError">
  <p>Please search for Lions or Giraffes!</p>
  </div>
  <div v-if="!images" class="lion-container">
    <div class="mane-top-wrapper">
    <div class="mane-top l-mane-top"></div>
    <div class="mane-top middle-mane-top"></div>
    <div class="mane-top r-mane-top"></div>
    </div>
    <div class="mane-face-wrapper">
      <div class="mane-left-wrapper">
        <div class="mane-left top-mane-l"></div>
        <div class="mane-left middle-mane-l"></div>
        <div class="mane-left bottom-mane-l"></div>
      </div>
    <div class="lion-head">
      <div class="eye">
        <div class="pupil"></div>
      </div>
      <div class="eye">
        <div class="pupil"></div>
      </div>
    </div>
      <div class="mane-right-wrapper">
        <div class="mane-right top-mane-r"></div>
        <div class="mane-right middle-mane-r"></div>
        <div class="mane-right bottom-mane-r"></div>
      </div>
    </div>
      <div class="mane-bottom-wrapper">
        <div class="mane-bottom l-mane-bottom"></div>
        <div class="mane-bottom middle-mane-bottom"></div>
        <div class="mane-bottom r-mane-bottom"></div>
      </div>
  </div>
<div v-if="images">
  <button id="previous-image-button" @click="handleClick">Previous Page</button>
  <button id="next-image-button" @click="handleClick">Next Page</button>
</div>
  </div>
</template>

<script>

// local imports
import ImageCard from '@/components/ImageCard.vue';
// external imports
import axios from 'axios';
// import config
import config from '../../public/config';

export default {
  // export this component
  name: 'Home',
  // register components imported from other files
  components: {
    ImageCard,
  },
  data() {
    return {
      tag: '',
      loading: false,
      images: null,
      page: 1,
      per_page: 25,
      searchError: false,
    };
  },
  methods: {
    async search() {
      const searchTerm = this.tag.toLowerCase();
      if (searchTerm.includes('giraffe') || searchTerm.includes('lion')) {
        this.images = null;
        this.searchError = false;
        this.loading = true;
        const res = await this.getPhotos();
        this.images = res.data.photos.photo;
        this.pages = res.data.photos.pages;
        this.loading = false;
      } else {
        this.images = null;
        this.searchError = true;
        this.tag = '';
      }
    },
    getPhotos() {
      return axios({
        method: 'get',
        url: 'https://api.flickr.com/services/rest',
        params: {
          method: 'flickr.photos.search',
          api_key: config.api_key,
          tags: this.tag,
          extras: 'url_n, owner_name, date_taken, views',
          page: this.page,
          format: 'json',
          nojsoncallback: 1,
          per_page: this.per_page,
          safe_search: 3,
        },
      });
    },
    handleSelect(e) {
      this.per_page = e.target.value;
      if (this.images) {
        this.search();
      }
    },
    handleClick(e) {
      if (e.target.id === 'previous-image-button' && this.page > 1) {
        this.page -= 1;
        this.search();
      } else if (e.target.id === 'next-image-button' && this.page < this.pages) {
        this.page += 1;
        this.search();
      }
      window.scrollTo(0, 0);
    },
  },
};
</script>

<style>

html {
  background-color: whitesmoke;
  height: 100vh;
}
.home {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-family: 'Poppins', sans-serif;
  letter-spacing: 1px;
  padding: 10px;
}

.header-info {
  width: 100vw;
  display: flex;
  justify-content: space-between;
}
h1 {
  font-size: 80px;
  margin: 0px;
}

form {
  margin-left: 100px;
}
button {
  margin-left: 5px;
}

.select-container {
  margin-right: 100px;
  margin-left: 10px
}

.image-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  padding: 0px
}

img {
  height: 300px;
  width: 400px;
  margin: 10px;
  border-bottom: 0 none;
  box-shadow: 0 1px 5px rgba(0, 0, 0, 0.46);
}

li {
  list-style: none;
}
.lion-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.mane-top-wrapper {
  display: flex;
  justify-content: center;
}
.mane-top {
  width: 0;
  height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-bottom: 80px solid brown;
  border-radius: 20%;
}
.l-mane-top {
  transform: rotate(-10deg)
}
.middle-mane-top {
  margin-bottom: 5px
}
.r-mane-top {
  transform: rotate(10deg)
}
.mane-left {
  width: 0;
  height: 0;
  border-top: 50px solid transparent;
  border-bottom: 50px solid transparent;
  border-right: 80px solid brown;
  border-radius: 20%;
}
.top-mane-l {
  transform: rotate(10deg)
}
.middle-mane-l {
margin-right: 5px
}
.bottom-mane-l {
  transform: rotate(-10deg)
}
.mane-face-wrapper {
  display: flex;
}
.lion-head {
  display: flex;
  justify-content: center;
  height: 300px;
  width: 300px;
  border-radius: 50px;
  background-color:goldenrod
}
.mane-right {
  width: 0;
  height: 0;
  border-top: 50px solid transparent;
  border-bottom: 50px solid transparent;
  border-left: 80px solid brown;
  border-radius: 20%;
}
.top-mane-r {
  transform: rotate(-10deg)
}
.middle-mane-r {
  margin-left: 5px
}
.bottom-mane-r {
  transform: rotate(10deg)
}

.mane-bottom-wrapper {
  display: flex;
  justify-content: center;
}
.mane-bottom {
  width: 0;
  height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-top: 80px solid brown;
  border-radius: 20%;
}
.l-mane-bottom {
  transform: rotate(10deg)
}
.r-mane-bottom {
transform: rotate(-10deg)
}
.eye {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50px;
  width: 50px;
  background-color: whitesmoke;
  border-radius: 50%;
  margin: 80px 20px;
}
.pupil {
  height: 20px;
  width: 38px;
  background-color: black;
  border-radius: 50%;
  /* margin-bottom: 2px; */
  transform: rotate(90deg);
  animation: eyes-animation 2s infinite;
}

@keyframes eyes-animation {
  0% {
    margin-left: -15px
  }
    50% {
    margin-left: 15px
  }
    100% {
    margin-left: -15px
  }
}

</style>
