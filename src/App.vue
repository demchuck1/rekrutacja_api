<template>
  <div id="app">
    <form @submit.prevent="getResultsFromApi">
      <div class="input-wrapper">
        <img src="./assets/search.svg">
        <input type="text" v-model="searchString" placeholder="Wszykuwana fraza" minlength="3" required>
      </div>      
      <button type="submit">Szukaj</button>
    </form>
    <div class="pagination-wrapper" v-if="!fetchingData">
      <jw-pagination :items="resultsArray" @changePage="onChangePage" :disableDefaultStyles="disableDefaultStyles" :labels="customLabels"></jw-pagination> 
    </div>
    <div class="loader-wrapper" v-if="fetchingData">
      <div class="loader-title">Trwa ładowanie</div>
      <div class="loader">
      <div class="wrapper">
        <div class="loader">
          <div class="wave top-wave">
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
          </div>
          <div class="wave bottom-wave">
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
          </div>
        </div>
      </div>
    </div>
    </div>
    <div class="result-info" v-if="fetchMessage != ''">
      {{ fetchMessage }}
    </div>
    <div class="results-list-wrapper" v-if="partedResultsArray.length > 0 && !fetchingData">
      <div class="results-header result-header-sticky">
        <div class="results-name">
          Nazwa repozytorium
        </div>
        <div class="results-url">
          URL repozytorium
        </div>
      </div>
      <ul class="results-list">
        <li v-for="(element,index) in partedResultsArray" :key="index" class="results-header">
          <div class="results-name">
            <div class="results-title">{{ element.package.name}}</div>
            <div class="results-desc"> {{ element.package.description}}</div>
          </div>
          <div class="results-url">
            <a :href="element.package.links.repository" target="_blank">{{ element.package.links.repository }}</a>
          </div>
        </li>
      </ul>
    </div>
    <div class="pagination-wrapper" v-if="!fetchingData">
      <jw-pagination :items="resultsArray" @changePage="onChangePage" :disableDefaultStyles="disableDefaultStyles" :labels="customLabels"></jw-pagination> 
    </div>
      <!-- <HelloWorld msg="Welcome to Your Vue.js App"> -->
  </div>
</template>

<script>

import axios from 'axios'

const customLabels = {
    first: '<<',
    last: '>>',
    previous: '<',
    next: '>'
};

const disableDefaultStyles = true;

export default {
  name: 'App',
  data() {
    return {
      searchString: 'react',
      resultsArray: [],
      partedResultsArray: [],
      resultsCount: 0,
      fetchingData: false,
      customLabels,
      disableDefaultStyles,
      fetchMessage: ''
    }
  },

  methods: {
    getResultsFromApi(){
      let results;
      this.fetchingData = true;
      this.fetchMessage = '';
      axios.get(this.API_URL, { headers: { 'Content-Type': 'application/json' }})
      .then(response => (
        this.fetchingData = false,
        this.resultsArray = [...response.data.results],
        this.resultsCount = response.data.total,
        this.fetchMessage = `Wyszukano ${this.resultsCount} elementów`
        )
      ).
      catch(error => {
        this.fetchingData = false,
        this.fetchMessage = error.response.data.message
      })
    },

    onChangePage(partedResultsArray) {
        this.partedResultsArray = partedResultsArray;
    }
    
  },

  computed: {
    resultsPageCount() {
      return Math.ceil(this.resultsCount / 25);
    },
    API_URL() {
      return `https://api.npms.io/v2/search?q=${this.searchString}&size=200`
    },
    
  }
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
  font-size: 18px;  
  color: #000;
  font-family: 'Quicksand', sans-serif;
  font-weight: 400;
}
#app {
  width: 100%;
  max-width: 1260px;
  padding: 0 30px;
  margin: 60px auto 0;
}

form {
  display: flex;
  align-items: center;
  gap: 15px;
  justify-content: center;

  button {
    text-transform: uppercase;
    font-weight: bold;
    color: #fff;
    background: linear-gradient(90deg, rgba(255,0,116,1) 0%, rgba(255,53,53,1) 100%); 
    padding: 0 35px;
    &:hover{
      cursor: pointer;
    }
  }

  input,button {
    height: 50px;
    border-radius: 4px;
    border: 0;
  }

  
  input {
    width: 100%;
    box-shadow: 0 0 20px rgba(0,0,0,.1);
    padding: 0 15px 0 45px;
    &:focus {
      outline: 0;
    }

  }

  img{
    width: 20px;
    height: 20px;
    position: absolute;
    left: 15px;
    top: 50%;
    transform: translateY(-50%);
    opacity: .4;
  }
}

.input-wrapper{
  position: relative;
  flex: 1;
  &:before {
    content: '';
    width: 40px;
    height: 40px;
  }    
}


.results-list{
  list-style: none;
  padding: 0;
  margin-top: 0;
  
  > li {
    padding: 10px 0px;
    margin-bottom: 0px;
    border-bottom: 1px solid rgb(236, 236, 236);
  }

  a{
    text-decoration: none;
  }
}

.results-list-wrapper{
  margin: 30px 0;
  border: 1px solid #000;
  margin: 0;
}

.results-header{
  display: flex;
  gap: 20px;
  text-align: left;
  > * {
    flex: 1;
    padding: 10px 15px;
  }
}


.result-header-sticky {
  border-bottom: 1px solid #000;
  position: sticky;
  top: 0;
  background: #fff;
  > * {
    padding: 20px 15px;
  }
}

.results-title{
  font-weight: bold;
  font-size: 20px;
  margin-bottom: 5px;
}

.results-desc{
  font-size: 14px;
}

.wrapper{
  display: flex;
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  justify-content: center;
  align-items: center;
  background: white;
}

.loader-wrapper{
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 60px 0;
    flex-direction: column;
}

.loader{
  display: flex;
  position: relative;
  width: 250px;
  height: 88px;
}

.loader-title{
  margin-bottom: 40px;
}

.wave{
  display: flex;
  justify-content: space-between;
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  perspective: 100px;
}

.wave > div{
  position: relative;
  width: 16px;
  height: 16px;
  border-radius: 100%;
}

.wave > div::before{
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: #aaaaaa;
  border-radius: 50%;
}

.top-wave > div::before {
  background-color: #ca0018;
}

.top-wave > div{
  animation: move 3s ease-in-out infinite reverse;
  
 }
 
.top-wave > div::before{
  animation: grow 3s linear infinite reverse; 
}

.bottom-wave > div{
  animation: move 3s ease-in-out infinite;
 }
 
.bottom-wave > div::before{
  animation: grow 3s linear infinite;
}

.wave > div:nth-child(10){
  animation-delay: 0s;
}
.wave > div:nth-child(9){
  animation-delay: -0.1s;
}
.wave > div:nth-child(8){
  animation-delay: -0.2s;
}
.wave > div:nth-child(7){
  animation-delay: -0.3s;
}
.wave > div:nth-child(6){
  animation-delay: -0.4s;
}
.wave > div:nth-child(5){
  animation-delay: -0.5s;
}
.wave > div:nth-child(4){
  animation-delay: -0.6s;
}
.wave > div:nth-child(3){
  animation-delay: -0.7s;
}
.wave > div:nth-child(2){
  animation-delay: -0.8s;
}
.wave > div:nth-child(1){
  animation-delay: -0.9s;
}


.bottom-wave > div:nth-child(10){
  animation-delay: 0.75s;
}
.bottom-wave > div:nth-child(9){
  animation-delay: 0.65s;
}
.bottom-wave > div:nth-child(8){
  animation-delay: 0.55s;
}
.bottom-wave > div:nth-child(7){
  animation-delay: 0.45s;
}
.bottom-wave > div:nth-child(6){
  animation-delay: 0.35s;
}
.bottom-wave > div:nth-child(5){
  animation-delay: 0.25s;
}
.bottom-wave > div:nth-child(4){
  animation-delay: 0.15s;
}
.bottom-wave > div:nth-child(3){
  animation-delay: 0.05s;
}
.bottom-wave > div:nth-child(2){
  animation-delay: -0.05s;
}
.bottom-wave > div:nth-child(1){
  animation-delay: -0.15s;
}


@keyframes move{
  0%{
    transform: translateY(0px);
  }
  25%{
    transform: translateY(88px);
  }
  50%{
    transform: translateY(0px);
  }
  75%{
    transform: translateY(88px);
  }
  100%{
    transform: translateY(0px);
  }

}

@keyframes grow{
  0%, 50%, 75%, 100% {
    transform: scaleX(0.7) scaleY(0.7);
  }
  10%, 60% {
    transform: scaleX(1) scaleY(1);
  }
  35%, 85% {
    transform: scaleX(0.4) scaleY(0.4);
  }
}

.result-info{
  margin-bottom: 20px;
}

.pagination{
  display: flex;
  justify-content: center;
  gap: 20px;
  list-style: none;
  padding: 0;
  margin: 25px 0;
  li{
    height: 50px;
    line-height: 50px;
    &.active{
      a {
        color: red;
      }
    }
    a{
      display: block;
      &:hover{
        cursor: pointer;
      }
    }
  }
}

</style>
