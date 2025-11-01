<template>
  <header>
    <div class="badge" v-if="solved"><span>🎉 Risolto</span></div>
  </header>
</template>


<script>
export default {
  name: 'AppHeader',
  props: {solved: {type: Boolean, default: false}}
}
</script>