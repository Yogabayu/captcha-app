<template>
  <div class="captcha-container">
    <div id="captcha" v-html="captchaHtml"></div>
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
      captchaHtml: '',
    };
  },
  methods: {
    async fetchCaptcha() {
      try {
        const response = await axios.post('https://info.dipendajatim.go.id/logic_pkb.php?act=captcha', {}, {
          headers: {
            'Accept': 'text/html',
          },
        });
        // Check if the response is in HTML format
        if (response.headers['content-type'].includes('text/html')) {
          this.captchaHtml = response.data;
        } else {
          console.error('Unexpected content type:', response.headers['content-type']);
        }
      } catch (error) {
        console.error('Error fetching CAPTCHA:', error);
      }
    },
    focusNextField(event, nextFieldId) {
      if (event.keyCode === 13) {
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
