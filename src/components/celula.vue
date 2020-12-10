<template>
  <div class="celula" :class="{viva: viva}">
  </div>
</template>

<script>
export default {
  name: 'Celula',
  props: {
    viva: {
      type: Boolean,
      default: false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.celula {
  background: white;
  display: inline-block;
  width: 20px;
  height: 20px;
  border-top: 1px solid #777;
  border-left: 1px solid #777;
}
.viva {
  background: #00D3FF;
  border-top: 1px solid #123660;
  border-left: 1px solid #123660;
}
</style>
