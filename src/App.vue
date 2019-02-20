<template>
  <div id="app">
   <input v-model="name" placeholder="name">
   <input v-model="surname" placeholder="surname">
    <button @click="add(name, surname)">Add person</button>
    <button @click="getPerson()">Get person</button>
  </div>
</template>

<script>
  import idbs from './api/indexedDBService'

export default {
  data() {
    return {
      name: '',
      surname: '',
    }
  },
  created() {

  },
  methods: {
    add() {
      let index = Math.random();
      let indexedDB = window.indexedDB || window.mozIndexedDB || window.webkitIndexedDB || window.msIndexedDB

      let open = indexedDB.open('db-name', 1,)

      open.onupgradeneeded = function() {
        let db = open.result
        db.createObjectStore('objectStoreName', { autoIncrement: true });
      }

      open.onsuccess = function() {
        let db = open.result
        let tx = db.transaction('objectStoreName', 'readwrite')
        let store = tx.objectStore('objectStoreName')
        store.clear();
        store.add({ firstname: 'Vova', lastname: 'Doe', age: index })

        tx.oncomplete = function() {
          db.close()
        }
      }
    },

    getPerson() {
      let indexedDB = window.indexedDB || window.mozIndexedDB || window.webkitIndexedDB || window.msIndexedDB
      let open = indexedDB.open('db-name', 1);

      open.onsuccess = function() {
        let db = open.result
        let transaction = db.transaction('objectStoreName', 'readonly');
        let objectStore = transaction.objectStore('objectStoreName');
        let request = objectStore.get(10);

        request.onerror = function (e) {
          // Handle errors!
        };
        request.onsuccess = function (e) {
          // Do something with the request.result!
          alert("Name of a person is " + request.result.firstname);
        };
      }
    },
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
