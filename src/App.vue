<template>
  <div id="app">
    
  </div>
</template>

<script> 
  import Firebase from 'firebase'

  // Initialize Firebase
  let config = {
    apiKey: "AIzaSyD6dEqbdzWHrulN9lZC5jEE5cB0q8DlyKI",
    authDomain: "nutrition-meter.firebaseapp.com",
    databaseURL: "https://nutrition-meter.firebaseio.com",
    projectId: "nutrition-meter",
    storageBucket: "nutrition-meter.appspot.com",
    messagingSenderId: "977781940637"
  };
  
  let app = firebase.initializeApp(config);
  let db = app.database();
  let ingredientsRef = db.ref('nutrition-meter')

export default {
  name: 'app',
  firebase: {
    ingredients: ingredientsRef
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
