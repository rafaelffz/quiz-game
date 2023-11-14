<template>
  <section class="score">
    Player <span>{{ this.winAnswers }}</span> x <span>{{ this.loseAnswers }}</span> Machine
    
    <div class="reset" v-if="this.winAnswers || this.loseAnswers > 0">
      <button type="button" @click="resetScore()">Reset Score</button>
    </div>
  </section>

</template>

<script>
export default {
  props: {
    winAnswers: {
      type: Number
    },
    loseAnswers: {
      type: Number
    }
  },
  methods: {
    resetScore() {
      this.$emit('resetScore')
    }
  },
}
</script>

<style lang="scss">
section.score {
  border-bottom: 1px solid #000;
  padding: 24px;
  font-size: 18px;

  span {
    padding: 8px;
    font-weight: bold;
    border: 1px solid #000;
  }

  .reset {
    button {
      margin-top: 32px;
      height: 40px;
      min-width: 120px;
      padding: 0 16px;
      color: #fff;
      background-color: #1867c0;
      border: 1px solid #1867c0;
      cursor: pointer;
    }
  }
}
</style>