<template>
  <div class="loading-container" v-if="show">
    <div class="lds-ripple"><div></div><div></div></div>
  </div>
</template>

<script>
export default {
  name: 'LoadingScreen',

  data() {
    return {
      
    };
  },

  mounted() {
    
  },

  methods: {
    
  },
  computed: {
    show(){
      return this.$store.state.loading
    }
  }
};
</script>

<style lang="scss" scoped>
  .loading-container {
    position: absolute;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #000;
    z-index: 1000;
    opacity: 0.8;
      .lds-ripple {
        display: inline-block;
        position: relative;
        width: 80px;
        height: 80px;
      }
      .lds-ripple div {
        position: absolute;
        border: 4px solid #fff;
        opacity: 1;
        border-radius: 50%;
        animation: lds-ripple 1s cubic-bezier(0, 0.2, 0.8, 1) infinite;
      }
      .lds-ripple div:nth-child(2) {
        animation-delay: -0.5s;
      }
      @keyframes lds-ripple {
        0% {
          top: 36px;
          left: 36px;
          width: 0;
          height: 0;
          opacity: 1;
        }
        100% {
          top: 0px;
          left: 0px;
          width: 72px;
          height: 72px;
          opacity: 0;
        }
      }
  }
</style>