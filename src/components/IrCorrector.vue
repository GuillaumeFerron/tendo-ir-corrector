<template>
  <div class="w-100">
    <div class="row">
      <h5>Calculator</h5>
      <div class="col-12 col-lg-6">
        <div>
          <label for="previous-bir">Previous BIR (%)</label>
          <input type="number" class="form-control" step=".01" id="previous-bir" v-model="data.previousBir">
        </div>
        <div class="mt-3">
          <label for="corrected-bir">Corrected BIR (%)</label>
          <input type="number" class="form-control" step=".01" id="corrected-bir" v-model="data.correctedBir">
        </div>
        <div class="mt-3">
          <label for="pv">Loan PV (Principal Value)</label>
          <input type="number" class="form-control" step=".01" id="pv" v-model="data.pv">
        </div>
        <div class="mt-3">
          <label for="tenure">Loan Tenure (months)</label>
          <input type="number" class="form-control" step="1" id="tenure" v-model="data.tenure">
        </div>
      </div>
      <div class="col-12 col-lg-6">
        <div>
          <label for="previous-interest">Previous Interest&emsp;</label>
          <small><i>{{ data.pv || 0 }} * {{ data.previousBir || 0 }}%
              * {{
              data.tenure || 0
              }}</i></small>
          <input type="number" class="form-control" disabled id="previous-interest" :value="previousInterest">
        </div>
        <div class="mt-3">
          <label for="corrected-interest">Corrected Interest&emsp;</label>
          <small><i>{{ data.pv || 0 }} * {{ data.correctedBir || 0 }}%
              * {{
              data.tenure || 0
              }}</i></small>
          <input type="number" class="form-control" disabled id="corrected-interest" :value="correctedInterest">
        </div>
        <div class="mt-3">
          <label for="exemption">Exemption&emsp;</label>
          <small><i>{{ previousInterest }} - {{ correctedInterest }}</i></small>
          <input type="number" class="form-control" disabled id="exemption" :value="exemption">
        </div>
        <div class="mt-3">
          <p class="py-1"></p>
          <button class="form-control btn btn-light" @click="clear">Clear</button>
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
              PV: {{ (data.pv || 0) }}<br>
              Previous BIR: {{ (data.previousBir || 0) }}%<br>
              Corrected BIR: {{ (data.correctedBir || 0) }}%<br>
              Tenure (months): {{ (data.tenure || 0) }}<br>
              Previous Interest: {{ (data.pv || 0) }} * {{ (data.previousBir || 0) }}% * {{ (data.tenure || 0) }} = {{ (previousInterest || 0) }}<br>
              Corrected Interest: {{ (data.pv || 0) }} * {{ (data.correctedBir || 0) }}% * {{ (data.tenure || 0) }} = {{ (correctedInterest || 0) }}<br>
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
              PV: {{ (data.pv || 0) }}<br>
              Previous BIR: {{ (data.previousBir || 0) }}%<br>
              Corrected BIR: {{ (data.correctedBir || 0) }}%<br>
              Tenure (months): {{ (data.tenure || 0) }}<br>
              Previous Interest: {{ (data.pv || 0) }} * {{ (data.previousBir || 0) }}% * {{ (data.tenure || 0) }} = {{ (previousInterest || 0) }}<br>
              Corrected Interest: {{ (data.pv || 0) }} * {{ (data.correctedBir || 0) }}% * {{ (data.tenure || 0) }} = {{ (correctedInterest || 0) }}<br>
              Exemption: {{ (previousInterest || 0) }} - {{ (correctedInterest || 0) }} = {{ (exemption || 0) }}
            </code>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  const CACHE_KEY = 'TENDOPAY_IR_CORRECTOR'
  const FORCE_CACHE = true

  export default {
    data() {
      return {
        data: {
          previousBir: null,
          correctedBir: null,
          pv: null,
          tenure: null
        },
        cacheProcessed: false
      }
    },
    watch: {
      data: {
        deep: true,
        handler() {
          if (this.cacheProcessed) {
            this.storeCache()
          }
        }
      }
    },
    mounted() {
      this.restoreCache()
    },
    computed: {
      previousInterest () {
        return (Math.ceil(this.data.pv * (this.data.previousBir / 100) * this.data.tenure * 100) / 100) || 0
      },
      correctedInterest() {
        return (Math.ceil(this.data.pv * (this.data.correctedBir / 100) * this.data.tenure * 100) / 100) || 0
      },
      exemption() {
        return (this.previousInterest - this.correctedInterest) || 0
      }
    },
    methods: {
      storeCache() {
        const obj = {
          data: this.data
        }
        localStorage.setItem(CACHE_KEY, JSON.stringify(obj))
      },
      restoreCache() {
        const res = localStorage.getItem(CACHE_KEY)
        if (res) {
          let clear = {}
          try {
            clear = JSON.parse(res)
          } catch (err) {
            clear = {}
          }
          if (!FORCE_CACHE) {
            this.clear()
            console.log(`* IR CORRECTOR: Invalidated cache`)
          } else {
            const data = clear.data
            Object.keys(data || {}).forEach(_ => {
              if (data[_] != null) {
                this.data[_] = data[_]
              }
            })
            console.log(`* IR CORRECTOR: Restored cache`)
          }
        }
        this.cacheProcessed = true
      },
      clear() {
        Object.keys(this.data || {}).forEach(_ => {
          if (typeof this.data[_] === 'object') {
            this.data[_] = []
          } else {
            this.data[_] = null
          }
        })
        localStorage.removeItem(CACHE_KEY)
      }
    }
  }
</script>

<style scoped lang="scss">
</style>