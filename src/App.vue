<template>
  <div>
    <div v-if="!result" class="search-panel">
      <label for="search-bar">Bugün nasıl hissediyorsun?</label>
      <input v-model="sentence"  type="text" />
      <button @click="getResults">
        <img v-if="loading" src="@/assets/loading.svg" alt="" />
        GÖNDER
      </button>
      <p v-if="error" style="color:#ef4f4f">{{ errorMsg }}</p>
      <p v-else-if="loading">{{ msg }}</p>
      <p v-else></p>
    </div>
    <div
      v-else
      class="cp"
      style="display:flex;flex-direction:column;align-items:center"
    >
      <p style="margin-bottom:12px;z-index:11;font-size:18px;text-align:right;max-width:300px;line-spacing:1.3px">
        {{
          result.result === 0
            ? 'Seni üzgün gördüm biraz. Umarım her şey istediğin gibi olur. Bu şarkı senin için 😢'
            : 'Harika! Bugün işler yolunda gitmiş. O zaman bu şarkı sana gelsin! 🙃'
        }}
      </p>
      <div class="cover-panel">
        <img class="cover" :src="track.album.cover_url" alt="" />
        <h1>{{ track.name }}</h1>
        <h2>{{ track.album.artists.map((x) => x.name).join(', ') }}</h2>
        <a :href="'https://open.spotify.com/track/' + track.id" target="_blank">
          <img class="spotify" src="@/assets/spotify.svg" alt="" />
        </a>
      </div>
      <p @click="back" style="text-align:left !important">geri dön</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      sentence: null,
      result: null,
      track: {
        album: {
          artists: [
            {
              id: '4tuJ0bMpJh08umKkEXKUI5',
              name: 'Gracie Abrams',
            },
          ],
          cover_url:
            'https://i.scdn.co/image/ab67616d0000b27355c38bc34d1fe852f2657c2e',
          id: '2UZw04wDxLVceADw2Gi1Qy',
          name: 'minor',
          uri: 'spotify:album:2UZw04wDxLVceADw2Gi1Qy',
        },
        analyse: {
          acousticness: 0.742,
          analysis_url:
            'https://api.spotify.com/v1/audio-analysis/6fGSHpEipD1YjtLLChnBzW',
          danceability: 0.7,
          duration_ms: 176947,
          energy: 0.494,
          id: '6fGSHpEipD1YjtLLChnBzW',
          instrumentalness: 0,
          key: 4,
          liveness: 0.304,
          loudness: -4.908,
          mode: 1,
          speechiness: 0.0463,
          tempo: 132.943,
          time_signature: 4,
          track_href:
            'https://api.spotify.com/v1/tracks/6fGSHpEipD1YjtLLChnBzW',
          type: 'audio_features',
          uri: 'spotify:track:6fGSHpEipD1YjtLLChnBzW',
          valence: 0.229,
        },
        id: '6fGSHpEipD1YjtLLChnBzW',
        name: 'Friend',
      },
      loading: false,
      currentMessage: '',
      currentIndex: 0,
      dotCounter: 0,
      errorMsg: 'Bir şeyler ters gitti. Baştan anlat.',
      error: false,
      messages: [
        '',
        'Hmmm',
        'Bana biraz zaman ver lütfen',
        'Senin için en uygun müziği arıyorum',
        'Heh bitti sayılır',
        'Biraz daha bekleteceğim',
      ],
    };
  },
  computed: {
    msg() {
      let str = this.messages[this.currentIndex];
      if (!str) {
        return '';
      }
      let x = this.dotCounter % 4;
      while (x > 0) {
        x--;
        str += '.';
      }
      return str;
    },
  },
  methods: {
    back() {
      this.error = false;
      this.sentence = null;
      this.result = null;
      this.track = null;
      this.loading = false;
      this.currentMessage = '';
      this.currentIndex = 0;
      this.dotCounter = 0;
    },
    async getResults() {
      this.error = false;
      this.currentIndex = 0;
      const self = this;
      setInterval(() => {
        self.dotCounter++;
      }, 500);
      setInterval(() => {
        if (self.currentIndex < 4) self.currentIndex++;
      }, 3500);
      this.loading = true;
      try {
        const res = await axios.get(
          'http://0.0.0.0:5001/?sentence=' + this.sentence
        );
        this.result = res.data.result;
        this.track = res.data.track;
        if (this.result === null || this.track === null) {
          this.error = true;
        }
      } catch (error) {
        this.error = true;
      }
      this.loading = false;
    },
  },
};
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;700&display=swap');
svg path,
svg rect {
  fill: #ff6700;
}
*:focus {
  outline: none;
}
#app,
body,
html {
  height: 100%;
}
.search-panel {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.search-panel input {
  font-family: Poppins;

  padding: 16px 30px;
  width: 400px;
  border-radius: 100px;
  border: 1px solid #ccc;
  transition: 300ms;
  opacity: 0.75;
}
.search-panel input:focus {
  box-shadow: 0px 0px 15px rgb(0, 0, 0, 0.1);
  border: 1px solid rgb(201, 114, 79, 0.5);
  opacity: 1;
}
.search-panel label {
  letter-spacing: 1px;
  font-family: Poppins;
  font-weight: 500;
  margin-bottom: 24px;
  font-size: 23px;
  color: #4c553d;
}
.search-panel button {
  letter-spacing: 2px;
  font-family: Poppins;
  font-weight: 500;
  margin-top: 20px;
  padding: 14px 30px;
  border-radius: 100px;
  border: none;
  background: #ee9ca7; /* fallback for old browsers */
  background: -webkit-linear-gradient(
    to top,
    #e08f62,
    #cc7351
  ); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(
    to top,
    #e08f62,
    #cc7351
  ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  cursor: pointer;
  color: white;
  transition: 300ms;
  display: flex;
  align-items: center;
}
.search-panel button img {
  margin-right: 4px;
  width: 20px;
  height: 20px;
  display: inline-block;
}
.search-panel button:hover {
  box-shadow: 0px 0px 15px rgb(0, 0, 0, 0.04);
  transform: translateY(-3px);
}
.search-panel button:active {
  box-shadow: 0px 0px 15px rgb(0, 0, 0, 0.1);
  transform: translateY(3px);
}

.search-panel p {
  font-family: Poppins;
  font-size: 14px;
  font-weight: 400;
  margin-top: 20px;
}
.cover-panel {
  width: 300px;
  font-family: Poppins;
  font-weight: 500;
  text-align: center;
  background: #2c2c2c;
  border-radius: 10px;

  box-shadow: 0px 0px 55px rgb(0, 0, 0, 0.3);
  transition: 300ms;
}
.cp:hover {
  transform: scale(1.05);
}
* {
  box-sizing: border-box;
}
.cover-panel h1 {
  margin-top: 14px;
  font-size: 20px;
  margin-left: 8px;
  margin-right: 8px;
  color: rgb(255, 255, 255, 0.8);
}
.cover-panel h2 {
  margin-top: 6px;
  font-size: 14px;
  color: rgb(255, 255, 255, 0.6);
}
.cover-panel h3 {
  margin-top: 6px;
  font-size: 14px;
  color: rgb(255, 255, 255, 0.4);
}
.cover-panel .cover {
  width: 300px;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
}
.cover-panel .spotify {
  margin-top: 20px;
  width: 30px;
  border-radius: 50%;
  margin-bottom: 20px;
}
.cp {
  transition: 300ms;
}
.cp p {
  font-family: Poppins;
  font-size: 12px;
  margin-top: 4px;
  cursor: pointer;
}
.cp p:hover {
  text-decoration: underline;
}
</style>
