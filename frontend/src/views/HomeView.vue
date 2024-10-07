<template>
  <!--<div class="payment-complete">
    <h1>결제가 완료되었습니다.</h1>
  </div>-->
  <Main/>
</template>

<script>
  import Main from '../components/Main'

  export default {
    name: 'Home',

    components: {
      Main,
    },
  }
</script>

<style>
.payment-complete {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

h1 {
  font-size: 24px;
  color: #333;
  text-align: center;
}
</style>