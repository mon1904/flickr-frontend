<template>
  <div class="datalist">
    <image-slider>
      <div v-for="number in [currentNumber]">
        <img class="img" :src="imagesList[Math.abs(currentNumber) % imagesList.length]"
          v-on:mouseover="stopRotation"
          v-on:mouseout="startRotation"
        />
      </div>
      <p>
        <a @click="prev">Previous</a> || <a @click="next">Next</a>
      </p>
    </image-slider>
    <div class="container"><h1>Welcome to my Image Flickr</h1>
        <input class="input-box" type="text" v-model="search" placeholder="Search for image" />
      <ul class="media-list">
          <li class="media" v-for="flickrImage in filteredImage">
            <div class="media-left">
              <a v-bind:href="flickrImage.url" target="_blank">
                <img class="media-object" v-bind:src=flickrImage.url>
              </a>
            </div>
            <div class="media-body">
              <h4 class="media-heading">ID:{{flickrImage.id}}</h4>
              <h5><i>Title: {{flickrImage.title}}</i></h5>
            </div>
          </li>
        </ul>
      </div>
    </div>
</template>

<script>
  export default {
    name: 'Flickrlist',
    data() {
      return {
        flickrImages: [],
        search: '',
        imagesList: ['https://i.amz.mshcdn.com/vj4FmugFnErV2bVnLpejVLpp1oU=/1200x627/2013%2F12%2F12%2F3b%2FFlickr.a5abc.jpg', 'https://cdn.pixabay.com/photo/2015/08/09/10/19/flickr-881367_960_720.png', 'https://www.slrlounge.com/wp-content/uploads/2014/04/Flickr_-_Logo.jpg'],
        currentNumber: 0,
        timer: null,
      }
    },

    methods: {

      startRotation: function () {
        this.timer = setInterval(this.next, 3000);
      },

      stopRotation: function () {
        clearTimeout(this.timer);
        this.timer = null;
      },

      next: function () {
        this.currentNumber += 1
      },
      prev: function () {
        this.currentNumber -= 1
      },
    },
      created: function () {
        this.$http.get('https://api.flickr.com/services/rest/?method=flickr.photos.search&api_key=9b4650184d97fd9ca60af98e60f5de6d&format=json&nojsoncallback=1&text=cats&extras=url_o')
          .then(response => {
            this.flickrImages = response.data.photos.photo;
            for (let image of this.flickrImages) {
              var url = "https://farm" + image.farm + ".staticflickr.com/" + image.server + "/" + image.id + "_" + image.secret + ".jpg";
              image.url = url;
            }
            this.filteredImage = this.flickrImages;
          });
      },

      computed: {
        filteredImage: function () {
          return this.flickrImages.filter((x) => {
            return x.title.toLowerCase().includes(this.search.toLowerCase())
          });
        }
      },

  }

</script>
<style scoped>
  .media-object {
    width: 130px;
    padding: 10px;
  }
  .media {
    border-top: 1px solid lightgray;
    padding-top: 20px;
    border-style: double;
    background-color: white;
    border-style: solid;
    border-color: #8c8b8b;
    border-width:2px 1px 0 0;
    border-radius: 20px;
  }
  .img{
    height: 300px;
    width: 60%;
  }
  .img.media-object{
    height: 300px;
    width: 60%;
  }

  h1 {

    font-family: "Great Vibes", cursive;
    line-height:160px;
    font-weight: normal;
    margin-bottom: 0px;
    margin-top: 20px;
    text-align: center;
    background: -webkit-linear-gradient(#f44289 50%, blue 40%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }

  .input-box {
    padding: 5px;
    margin-bottom: 10px;
    width: 200px;
    border-width:2px 1px 1px 1px;
    border-radius: 5px;
  }
</style>
