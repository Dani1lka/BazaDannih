<template>
<div class="container">
  <form class="card" @submit.prevent="createPerson">
    <h2>Работа с базой данных</h2>
    <div class="card">
      <label for="name"></label>
      <input type="text" id="name" name="" v-model.trim="name">
    </div>
    <button class="btn primary" :disabled="name.length===0">Создать</button>
    <app-people-list :people="people"></app-people-list>
  </form>
</div>
</template>

<script>


import appPeopleList from './appPeopleList.vue'
export default {
  
  name: 'App',
  data(){
   return{
    name: '',
    people: [],
   }
  },
  methods:{
    async createPerson(){
      //this.name
      // https://bazadanih-b3b73-default-rtdb.firebaseio.com/people.json
      const response = await fetch('https://bazadanih-b3b73-default-rtdb.firebaseio.com/people.json',{
        method: 'POST',
        headers:{
          'Content-Type' : 'application/json'
        },
        body:JSON.stringify({firstName: this.name})
      }) 
      const firebaseData = await response.json()
      console.log(firebaseData);
      this.name = ''
    },
  },
  components:{appPeopleList}
}
</script>

<style>

</style>
