<template>
  <div class="w-100">
    <div class="row">
      <h5>Calculator</h5>
      <div class="col-12 col-lg-6">
        <div>
          <label for="previous-bir">Previous BIR (%)</label>
          <input type="number" class="form-control" step=".01" id="previous-bir" v-model="previousBir">
        </div>
        <div class="mt-3">
          <label for="corrected-bir">Corrected BIR (%)</label>
          <input type="number" class="form-control" step=".01" id="corrected-bir" v-model="correctedBir">
        </div>
        <div class="mt-3">
          <label for="pv">Loan PV (Principal Value)</label>
          <input type="number" class="form-control" step=".01" id="pv" v-model="pv">
        </div>
        <div class="mt-3">
          <label for="tenure">Loan Tenure (months)</label>
          <input type="number" class="form-control" step="1" id="tenure" v-model="tenure">
        </div>
      </div>
      <div class="col-12 col-lg-6">
        <div>
          <label for="previous-interest">Previous Interest&emsp;</label>
          <small><i>{{ pv || 0 }} * {{ previousBir || 0 }}%
              * {{
              tenure || 0
              }}</i></small>
          <input type="number" class="form-control" disabled id="previous-interest" :value="previousInterest">
        </div>
        <div class="mt-3">
          <label for="corrected-interest">Corrected Interest&emsp;</label>
          <small><i>{{ pv || 0 }} * {{ correctedBir || 0 }}%
              * {{
              tenure || 0
              }}</i></small>
          <input type="number" class="form-control" disabled id="corrected-interest" :value="correctedInterest">
        </div>
        <div class="mt-3">
          <label for="exemption">Exemption&emsp;</label>
          <small><i>{{ previousInterest }} - {{ correctedInterest }}</i></small>
          <input type="number" class="form-control" disabled id="exemption" :value="exemption">
        </div>
      </div>
    </div>
    <hr class="mt-4">
    <div class="row mt-3">
      <h5>Process</h5>
      <div class="my-2">
        <i>1. Apply an exemption of <b>PHP{{ exemption }}</b> to the user's account</i>
      </div>
      <div class="my-2">
        <i>2. The summary should be:</i><br>
        <div class="bg-light rounded p-2 mt-1">
          <code>
              PV: {{ (pv || 0) }}<br>
              Previous BIR: {{ (previousBir || 0) }}%<br>
              Corrected BIR: {{ (correctedBir || 0) }}%<br>
              Tenure (months): {{ (tenure || 0) }}<br>
              Previous Interest: {{ (pv || 0) }} * {{ (previousBir || 0) }}% * {{ (tenure || 0) }} = {{ (previousInterest || 0) }}<br>
              Corrected Interest: {{ (pv || 0) }} * {{ (correctedBir || 0) }}% * {{ (tenure || 0) }} = {{ (correctedInterest || 0) }}<br>
              Exemption: {{ (previousInterest || 0) }} - {{ (correctedInterest || 0) }} = {{ (exemption || 0) }}
            </code>
        </div>
      </div>
      <div class="my-2">
        <i>3. Add a comment to the user's account:</i><br>
        <div class="bg-light rounded p-2 mt-1">
          <code>
              Exemption applied for correction to the interest rate<br>
              <br>
              PV: {{ (pv || 0) }}<br>
              Previous BIR: {{ (previousBir || 0) }}%<br>
              Corrected BIR: {{ (correctedBir || 0) }}%<br>
              Tenure (months): {{ (tenure || 0) }}<br>
              Previous Interest: {{ (pv || 0) }} * {{ (previousBir || 0) }}% * {{ (tenure || 0) }} = {{ (previousInterest || 0) }}<br>
              Corrected Interest: {{ (pv || 0) }} * {{ (correctedBir || 0) }}% * {{ (tenure || 0) }} = {{ (correctedInterest || 0) }}<br>
              Exemption: {{ (previousInterest || 0) }} - {{ (correctedInterest || 0) }} = {{ (exemption || 0) }}
            </code>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { computed } from 'vue'

  const previousBir = defineModel('previousBir')
  const correctedBir = defineModel('correctedBir')
  const pv = defineModel('pv')
  const tenure = defineModel('tenure')

  const previousInterest = computed(() => {
    return (Math.ceil(pv.value * (previousBir.value / 100) * tenure.value * 100) / 100) || 0
  })

  const correctedInterest = computed(() => {
    return (Math.ceil(pv.value * (correctedBir.value / 100) * tenure.value * 100) / 100) || 0
  })

  const exemption = computed(() => {
    return (previousInterest.value - correctedInterest.value) || 0
  })
</script>

<style scoped lang="scss">
</style>