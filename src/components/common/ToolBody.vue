<template>
  <div class="card mb-0 w-75">
    <div class="card-body px-5">
      <h3>Tendo IR Corrector</h3>
      <IrCorrector/>
    </div>
  </div>
</template>

<script setup>
import IrCorrector from '../IrCorrector.vue'
</script>

<style lang="scss" scoped>
 .card {
    -webkit-box-shadow: 5px 5px 15px 5px rgba(0, 0, 0, 0.3);
    box-shadow: 5px 5px 15px 5px rgba(0, 0, 0, 0.3);
  }
</style>
