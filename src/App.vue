<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
  <div class="content">
    <table>
      <tbody>
        <tr v-for="element in todos" :key="element.id">
          <td>{{element.id}}</td>
          <td>{{element.codigo}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    // HelloWorld
  },
  data(){
    return{
      todos:null
    }
  },
  mounted(){
    this.getTodos();
  },
  methods:{
    getTodos(){
      axios.get('http://localhost:8000/api/Boletas')
        .then( response =>{
          console.log(response)
          this.todos = response.data
        }).catch(
          e => console.log(e)
        )
    }
  }


}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
