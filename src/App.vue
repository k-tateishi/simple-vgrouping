<template>
  <div id="app">
    <section class="section">
      <div class="container">
        <h1 class="is-size-1">{{ msg }}</h1>
        <information-component />
        <grouping-component />
      </div>
    </section>
    <footer-component />
  </div>
</template>

<script>
import InformatinComponent from './components/Information'
import GroupingComponent from './components/Grouping';
import FooterComponent from './components/Footer';
export default {
  name: 'app',
  components: {
    'information-component': InformatinComponent,
    'grouping-component': GroupingComponent,
    'footer-component': FooterComponent
  },
  data () {
    return {
      msg: '班分けアプリ'
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
