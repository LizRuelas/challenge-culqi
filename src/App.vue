<template>
  <h1 class="text-green-600 font-bold text-4xl text-left">Rick and Morty finder</h1>
  <div class="main">
    <div class="w-48 my-10">
      <img alt="" src="./assets/logo_2.png">
    </div>
  </div>

  <div class="grid grid-cols-2 gap-10" >
    <div class="w-96 ml-auto">
      <input 
          class="block w-full px-3 py-2 transition duration-100 ease-in-out border rounded shadow-sm focus:ring-2 focus:ring-blue-500 focus:outline-none focus:ring-opacity-50 disabled:opacity-50 disabled:cursor-not-allowed text-black placeholder-gray-400 bg-white border-gray-300 focus:border-blue-500 "
          placeholder="Find a character"
          type="text"
          v-model="name" 
          v-on:keyup="search"
        >
        <br>
      <span class="text-red-600 font-bold" >{{  errorMessage }}</span>
    </div>
        
    <div class="w-72">
      <select
        :disabled = disabledSelect
        id="status"
        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
        v-on:change="this.$data.status = $event.target.value"
        @change="selectStatus()"
        >
          <option :value="null" disabled selected>Status</option>
          <option value="alive">Alive</option>
          <option value="dead">Dead</option>
          <option value="unknown">Unknown</option>
        </select>
    </div>
        

  </div> 

  <div >
    <div class="my-14 grid grid-cols-2 gap-10">
      <div
      class="card grid grid-cols-2"
      v-for="(character, index) in info" :key="index"
      >
        <div>
          <img :src="character.image" alt="">
        </div>
       
        <div class="p-14 ">
          <h3 class="text-lg font-bold" >{{ character.name }}</h3>
          <p>{{ character.status }}- {{ character.species }}</p>
          <p>Last Know Location: {{ character.location.name }}</p>
          <p>First seen in: {{ character.origin.name }}</p>
        </div>
      </div>
    </div> 
  </div>
</template>

<script>
import axios from "axios";
import { VALIDATION_MSG } from '../utils/const'

export default {
  name: 'App',
  components: {},
  data () {
    return {
      errorMessage: null,
      disabledSelect: true,
      info: null,
      name: "",
      status: null
    }
  },
  watch: {},
  methods: {
    search() {
      console.log(this.name)
      if(!this.name) {
        this.errorMessage = VALIDATION_MSG.required
        this.disabledSelect = true
        this.info = ""
      } else if (this.name.length < 4) {
        this.errorMessage = VALIDATION_MSG.min
        this.disabledSelect = true
        this.info = ""
      } else if (this.name.length > 20) {
        this.errorMessage = VALIDATION_MSG.max
        this.disabledSelect = true
        this.info = ""
      } else {
        axios
        .get(`https://rickandmortyapi.com/api/character/?name=${this.name}`)
        .then(response => (this.info = response.data.results, this.errorMessage = "", this.disabledSelect = false, console.log(this.info)))
        .catch(error => {
          console.log(this.info)
          if (error) {
            this.errorMessage = "Este personaje no existe"
            this.disabledSelect = true
            this.info = ""
          }
          
        })
      }
    },
    selectStatus() {
      console.log(this.status)
      axios
      .get(`https://rickandmortyapi.com/api/character/?name=${this.name}&status=${this.status}`)
      .then(response => (this.info = response.data.results))
      .catch(error => console.log(error))
    }
  },
}
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin: 60px;
  }

  img {
    margin: auto;
    border-radius: 20px 0px 0px 20px;
    height: 100% !important;
    margin: 0px;
  }

  .main {
    display: flex;
    justify-content: center;
  }

  .card {
    border: 1px solid gray;
    border-radius: 20px;
  }
</style>
