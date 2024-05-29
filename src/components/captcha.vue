<template>
  <div class="captcha-container">
    <div id="captcha">
      <img :src="captchaImageUrl" alt="CAPTCHA Image" v-if="captchaImageUrl" />
    </div>
    <button @click="fetchCaptcha">Refresh CAPTCHA</button>
    <input id="txtnopol" @keypress="focusNextField($event, 'txtcaptcha')" />
    <input id="txtcaptcha" @keypress="focusNextField($event, 'btncari')" />
    <button id="btncari" @click="getInfo">Search</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      captchaImageUrl: '',
    };
  },
  methods: {
    // async fetchCaptcha() {
    //   try {
    //     const response = await axios.post('https://thingproxy.freeboard.io/fetch/https://info.dipendajatim.go.id/logic_pkb.php?act=captcha');
    //     if (response.headers['content-type'].includes('text/html')) {
    //       const parser = new DOMParser();
    //       const doc = parser.parseFromString(response.data, 'text/html');
    //       const captchaImgElement = doc.querySelector('img');
          
    //       if (captchaImgElement) {
    //         const relativeUrl = captchaImgElement.getAttribute('src');
    //         this.captchaImageUrl = 'https://thingproxy.freeboard.io/fetch/https://info.dipendajatim.go.id' + relativeUrl;
    //       }
    //     } else {
    //       console.error('Unexpected content type:', response.headers['content-type']);
    //     }
    //   } catch (error) {
    //     console.error('Error fetching CAPTCHA:', error);
    //   }
    // },
    async fetchCaptcha() {
      try {
        // Gunakan Thingproxy untuk menghindari CORS
        const proxyUrl = 'https://thingproxy.freeboard.io/fetch/';
        const targetUrl = 'https://info.dipendajatim.go.id/logic_pkb.php?act=captcha';
        const response = await axios.post(`${proxyUrl}${targetUrl}`);
        
        if (response.headers['content-type'].includes('text/html')) {
          const parser = new DOMParser();
          const doc = parser.parseFromString(response.data, 'text/html');
          const captchaImgElement = doc.querySelector('img');
          
          if (captchaImgElement) {
            const relativeUrl = captchaImgElement.getAttribute('src');
            // Tambahkan prefix Thingproxy ke URL relatif gambar CAPTCHA
            this.captchaImageUrl = `${proxyUrl}https://info.dipendajatim.go.id${relativeUrl}`;
          }
        } else {
          console.error('Unexpected content type:', response.headers['content-type']);
        }
      } catch (error) {
        console.error('Error fetching CAPTCHA:', error);
      }
    },
    focusNextField(event, nextFieldId) {
      if (event.key === 'Enter') {
        this.$nextTick(() => {
          const nextField = this.$el.querySelector(`#${nextFieldId}`);
          if (nextField) {
            nextField.focus();
          }
        });
      }
    },
    getInfo() {
      // Your logic to handle the search button click
    },
  },
  mounted() {
    this.fetchCaptcha();
    this.$nextTick(() => {
      const nopolField = this.$el.querySelector('#txtnopol');
      if (nopolField) {
        nopolField.focus();
      }
    });
  },
};
</script>

<style scoped>
.captcha-container {
  text-align: center;
  margin: 20px;
}
</style>
