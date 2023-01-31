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
    <app-loader v-if="loading"></app-loader>
    <app-people-list :people="people" @load="loadPeople" @remove="removePerson" v-else></app-people-list>
    
  </form>
</div>
</template>

<script>


import appPeopleList from './appPeopleList.vue'
import AppAlert from './AppAlert.vue'
import AppLoader from './AppLoader.vue'
import axios from 'axios'
export default {
  
  name: 'App',
  data(){
   return{
    name: '',
    people: [],
    alert: null,
    loading: false,
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
        this.loading = true
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
            this.loading = false
      }catch(error){
      this.alert = {
        type: 'danger',
        title: 'Ошибка',
        text: error.message,
      }
      this.loading = false
      }
    },
    async removePerson(id){
      try{
        let name = this.people.find(person => person.id === id).firstName
        await axios.delete(`https://bazadanih-b3b73-default-rtdb.firebaseio.com/people/${id}.json`)
        this.people = this.people.filter(person => person.id !== id)
        this.alert ={
          type:'primary',
          title: 'Успешно',
          text: `Пользователь с именем "${name}" был удален`,
        }
      }catch(error){
        this.alert('ALAAAAAAAAAAAAAAAAAAAARM')
      }
      
    },
  },
  components:{appPeopleList, AppAlert,AppLoader}
}
</script>

<style>

</style>
