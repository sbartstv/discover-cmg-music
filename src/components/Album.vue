<template>
  <div class="container-a">
       <v-layout row v-if="!noAlbums">
      <v-flex xs12>
        <img class="banner-image" :src="transformArtistBannerImage" :alt="artist.name">
<!--          <h3 class="banner">{{artist.name}}</h3> --> 
<div class="banner" >
   <h1 class="display-3">{{artist.name}}</h1>

</div>        
      </v-flex>
    </v-layout>




 <v-layout row>
       <v-flex xs12>
<!-- Breadcrumbs here -->
    <v-breadcrumbs large>
      <v-icon slot="divider">chevron_right</v-icon>
      <v-breadcrumbs-item
        v-for="item in breadcrumbs"
        :key="item.text"
        :href="item.link"
        :disabled="item.disabled">
        {{ item.text }}
      </v-breadcrumbs-item>
    </v-breadcrumbs>
      </v-flex>
    </v-layout>
    <v-container>
        

      <v-layout row>
        <v-flex xs2>
          <div class="album-artist" v-if="!noAlbums">
            <img :src="transformArtistAvatarImage" :alt="artist.name">
             <h1 class="subheading">{{artist.name}}</h1>
          </div>
        </v-flex>
        <v-flex xs1/>
        <v-flex xs9>
          <h3 v-if="noAlbums">No albums available to stream.</h3>
          <album-list v-else :albums="albums" :cl="cl" :artist="artist"></album-list>
          <rise-loader :loading="loading" color="#fff" v-if="!noAlbums"></rise-loader>
        </v-flex>
      </v-layout>
      
    </v-container>
  </div>

</template>

<script>
import RiseLoader from 'vue-spinner/src/RiseLoader';
import AlbumList from './AlbumList'
import { API_BASE_URI, cl } from '../utils';
export default {
  data() {
    return {
      artist: {},
      albums: [],
      items: [],
      parallaxImage:'',
      cl,
      API_BASE_URI,
      noAlbums: false,
      loading: false
    };
  },
  created() {
    console.log('Route:',this.$route);
    this.fetchAlbums(this.$route.params.artistId);
  },
  computed: {
    breadcrumbs (){
     return  [
          {
            text: "Search",
            link: '/browse/a',
            disabled: false
          },
          {
            text: this.artist.name,
            link: this.$route.path,
            disabled: false
          }
          
        ]


    },
    transformArtistAvatarImage() {
      return this.cl.url(this.artist.image, {
        width: 200,
        height: 200,
        gravity: 'auto:body',
        crop: 'fill',
        fetchFormat: 'auto',
        quality: 'auto',
        radius: '50:0:50:0',
        type: 'fetch',
        format:'png'
      });
    },
    transformArtistBannerImage() {
      
      //verlays/inverted-wave.png
      // let encoded = encodeURI('https://res.cloudinary.com/capitol-music-group/image/upload/w_1140,h_150,a_180/v1526912791/overlays/inverted-wave.png');

      let url =  this.artist.image;
      return this.cl.url(url, {
        width: '1400',
        height: 150,
        gravity: 'west',
        crop: 'lpad',
        aspectRatio: '16:9',
        background: 'white',
        //'auto:predominant',
        radius: '0:0:149:0',
        //predominant
        effect:'gradient_fade:symmetric_pad:0.05',
        // x:0.2,
        fetchFormat: 'auto',
        format:'png',
        quality: 'auto',
        type: 'fetch',
      });
    }
  },
  methods: {
    fetchAlbums: async function(artistId) {
      this.loading = true;
      const response = await fetch(`${API_BASE_URI}/releases/${artistId}`);
      const data = await response.json();
      this.albums = data.releases.release;
      if(this.albums.length < 1) {
        this.noAlbums = true;
        return;
      }
      this.artist = this.albums[0].artist;
      this.loading = false;
    }
  },
  components: {
    AlbumList,
    RiseLoader
  }
};
</script>

<style>
.album-artist {
  position: relative;
}
.album-artist img {
  width: 200px;
  height: 200px;
  margin: auto;
  display: block;
}
.album-artist h3 {
  text-align: center;
  margin-top: 10px;
}
.banner {
    position: absolute;
    top: 100px;
    left: 300px;
    color: #231F20;    
}
.banner-image {
  height: 150px !important;
  display: block !important;
}
</style>
