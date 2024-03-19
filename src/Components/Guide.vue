<template>
  <a-button class="btn-guide" type="dashed" @click="openGuideModal = true"> Guide </a-button>
  <a-modal v-model:open="openGuideModal" title="Guide" :footer="null" centered width="800px">
    <p>1. Copy files in smartgit</p>
    <p class="image"><img src="../assets/images/1.png" alt="" /></p>
    <p>2. Paste to Git input</p>
    <p class="image"><img src="../assets/images/2.png" alt="" /></p>
    <p>3. Copy logs in Filezilla</p>
    <p class="image"><img src="../assets/images/3.png" alt="" /></p>
    <p>4. Paste to Filezilla input</p>
    <p class="image"><img src="../assets/images/4.png" alt="" /></p>
    <p>5. Press compare button</p>
    <p class="image"><img src="../assets/images/5.png" alt="" /></p>
    <p>6. See result</p>
    <p class="image"><img src="../assets/images/6.png" alt="" /></p>
  </a-modal>
</template>
<script setup>
import { ref } from 'vue'

const openGuideModal = ref(false)
</script>

<style scoped>
.btn-guide {
  position: absolute;
  top: 10px;
  right: 10px;
}
p {
  margin: 10px 0;
}
p.image {
  text-align: center;
}
img {
  max-width: 80%;
}
</style>
