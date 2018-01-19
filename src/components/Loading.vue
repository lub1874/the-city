<template>
  <div class="loading" v-show="isOpen">
    <div class="spinner"></div>
  </div>
</template>

<style scoped>
  .loading {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background: rgba(255, 255, 255, 0.5);
  }

  .spinner {
    width: 3rem;
    height: 3rem;
    background: #1abc9c;
    opacity: 1;
    z-index: 10;
    position: absolute;
    left: 50%;
    top: 50%;
    -webkit-transform: translate(-50%, -50%);
    -moz-transform: translate(-50%, -50%);
    -ms-transform: translate(-50%, -50%);
    -o-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
    -webkit-animation: rotateplace 1.2s infinite ease-in-out;
    -o-animation: rotateplace 1.2s infinite ease-in-out;
    animation: rotateplace 1.2s infinite ease-in-out;
  }

  @-webkit-keyframes rotateplane {
    0% {
      -webkit-transform: perspective(120px)
    }
    50% {
      -webkit-transform: perspective(120px) rotateY(180deg)
    }
    100% {
      -webkit-transform: perspective(120px) rotateY(180deg) rotateX(180deg)
    }
  }

  @keyframes rotateplane {
    0% {
      transform: perspective(120px) rotateX(0deg) rotateY(0deg);
      -webkit-transform: perspective(120px) rotateX(0deg) rotateY(0deg)
    }
    50% {
      transform: perspective(120px) rotateX(-180.1deg) rotateY(0deg);
      -webkit-transform: perspective(120px) rotateX(-180.1deg) rotateY(0deg)
    }
    100% {
      transform: perspective(120px) rotateX(-180deg) rotateY(-179.9deg);
      -webkit-transform: perspective(120px) rotateX(-180deg) rotateY(-179.9deg);
    }
  }
</style>

<script>
  export default {
    name: 'loading',
    props: ['isOpen'],
    data () {
      return {
        isLoading: true
      }
    }
  }
</script>
