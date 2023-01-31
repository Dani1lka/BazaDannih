<template>
<div class="container">
  <app-alert :alert="alert" @close="alert=null"></app-alert>
  <form class="card" @submit.prevent="createPerson">
    <h2>Работа с базой данных</h2>
    <div class="card">
      <label for="name"></label>
      <input type="text" id="name" name="" v-model.trim="name">
    </div>
    <button class="btn primary" :disabled="name.length===0">Создать</button>
    <app-people-list :people="people" @load="loadPeople" @remove="removePerson"></app-people-list>
    
  </form>
</div>
</template>

<script>


import appPeopleList from './appPeopleList.vue'
import AppAlert from './AppAlert.vue'
import axios from 'axios'
export default {
  
  name: 'App',
  data(){
   return{
    name: '',
    people: []
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
     async loadPeople(){
      try{
      const {data} = await axios.get('https://bazadanih-b3b73-default-rtdb.firebaseio.com/people.json')
      if(!data){
        throw new Error("Список пуст")
      }
      this.people = Object.keys(data).map(key=>{
              return{
                id:key,
                firstName: data[key].firstName,
              }
            })
      }catch(error){
      this.alert = {
        type: 'danger',
        title: 'Ошибка',
        text: error.message,
      }
      }
    },
    async removePerson(id){
      await axios.delete(`https://bazadanih-b3b73-default-rtdb.firebaseio.com/people/${id}.json`)
      this.people = this.people.filter(person => person.id !== id)
    },
  },
  components:{appPeopleList}
}
</script>

<style>

</style>
