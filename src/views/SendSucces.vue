<template>
  <div class="about">
    <p class="about__p">Заявка отправлена.</p>
    <router-link class="link" to="/">отправить ещё одну</router-link>
    <img src="../assets/success_img.png" alt="">
  </div>
</template>

<style scoped lang="scss">
  .about{
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .about__p {
    margin-bottom: 30px;
  }
  .link{
    margin-bottom: 30px;
  }
</style>