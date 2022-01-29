<template>
  <div class="main">
    
  <template v-if="!loading && userData && userData.length">
  <table>
    <tr>
    <th>Serial No. <button v-on:click="sorting('serial')">Sort</button> </th>
    <th>Picture</th>
    <th>Name <button v-on:click="sorting('name')">Sort</button> </th>
    <th>Dob <button v-on:click="sorting('dob')">Sort</button></th>
    <th>Email <button v-on:click="sorting('email')">Sort</button></th>
    <th>Location</th>
    <th>Phone</th>
  </tr>
  
  <template v-for="(item, index) in userData" :key="index">
    <!-- <p>{{item}}</p> -->
    <tr>
      <th>  {{ item.serial_no }} </th>
      <th> <img :src = "item.picture" alt=""> </th>
      <th> {{fullName(item.name)}} </th>
      <th>  {{formatDob(item.dob)}} </th>
      <th> {{item.email}} </th>
      <th> {{formatLocation(item.location)}} </th>
      <th> {{ item.phone }} </th>
    </tr>
  </template>
  

  </table>
  
    
  </template>

  <p v-if="loading">
   Still loading..
  </p>
  <p v-if="error"></p>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue';
  
  let userData = ref(null) as any;
  const loading = ref(true);
  const error = ref(null);
  let dobSort = ref(true);
  let sortName = ref(true);
  let serialSort = ref(true);
  let emailSort = ref(true);
  interface User{
    serial_no: any,
    picture: any,
    name: any,
    dob: any,
    email: any,
    location: any,
    phone: any
  }

  
  const fullName = (name: any) => `${name.first} ${name.last}`
  const formatLocation = (location: any) => `${location.street.number} ${location.street.name} ${location.city} ${location.country}`
  const formatDob = (dob: any) => `${dob.date.split('T')[0]}`

  const sorting = (type: string) => {
    console.log(type)
    if(type == "serial"){
      serialSort.value = !serialSort.value
     if(serialSort.value){
       userData.value.sort((a: any, b: any) => {
        return a.serial_no - b.serial_no;
      });
     }
     else{
       userData.value.sort((a: any, b: any) => {
        return b.serial_no - a.serial_no;
      });
     }
    }
    else if(type == "dob"){
      dobSort.value = !dobSort.value;
      if(dobSort.value){
        userData.value.sort((a: any, b: any) => {
          return new Date(b.dob.date) - new Date(a.dob.date);
        });
      }
      else{
        userData.value.sort((a: any, b: any) => {
          return new Date(a.dob.date) - new Date(b.dob.date);
        });
      }
    }
    else if(type == "name"){
      sortName.value = !sortName.value;
      if(sortName.value){
        userData.value.sort((a:any, b:any) => {
          return a.name.first.localeCompare(b.name.first)
        })
      }
      else{
        userData.value.sort((a:any, b:any) => {
          return b.name.first.localeCompare(a.name.first)
        })
      }
      
    }
    else if(type == "email"){
      emailSort.value = !emailSort.value;
      if(emailSort.value){
        userData.value.sort((a:any, b:any) => {
          return a.email.localeCompare(b.email)
        })
      }
      else{
        userData.value.sort((a:any, b:any) => {
          return b.email.localeCompare(a.email)
        })
      }
      
    }
  }

  function fetchData() {
    loading.value = true;
    return fetch('https://randomuser.me/api/?results=50', {
      method: 'get',
      headers: {
        'content-type': 'application/json'
      }
    })
      .then(res => {
        if (!res.ok) {
          const error = new Error(res.statusText);
          throw error;
        }

        return res.json();
      })
      .then(json => {
        const User: User[] = json.results.map((el: Element, index: any) => {
          const serial_no: any = index + 1;
          const picture: any =  el.picture.thumbnail;
          const name: any =  el.name;
          const dob: any =  el.dob;
          const email: any = el.email;
          const location: any = el.location;
          const phone: any = el.phone;
          return {serial_no, picture, name, dob, email, location, phone}
        })
        userData.value = User.reverse();
        
        
      })
      .catch(err => {
        error.value = err;
        if (err) {
          return err
        }
      })
      .then(() => {
        loading.value = false;
      });
  }

  onMounted(() => {
    fetchData();
  });
  

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
table {
  border-collapse: collapse;
  width: 100%;
}

td, th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #efefef;
}
</style>
