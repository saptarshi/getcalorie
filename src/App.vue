<template>
  <div id="app" class="container">
    <div class="page-header">
      <h1>Calculate calories <small>Enter recipe ingredients one at a time</small></h1>
    </div>
    <div class="panel panel-default">
      <div class="panel-body">
        <form id="addToRecipe" class="form-inline hide-in-print" v-on:submit.prevent="addToRecipe">
          <div class="form-group">
            <label for="recipeIngredient">Name</label>
            <input type="text" id="recipeIngredient" class="form-control" v-model="newIngredient.Item" v-on:click.once="enableAutocomplete" v-on:change="changeFocus" autocomplete="off" placeholder="Type Ingredient Name">
          </div>
          <div class="form-group">
            <label for="quantity">Quantity</label>
            <input type="number" id="quantity" class="form-control" v-model="newIngredient.Quantity">
          </div>
          <input type="submit" class="btn btn-primary" value="Add to List">
        </form>
        <h4 v-if="recipe.ingredients.length > 0">List of recipe ingredients</h4>
        <table class="table table-striped" v-if="recipe.ingredients.length > 0">
          <thead>
            <tr>
              <th>
                Item
              </th>
              <th>
                Quantity
              </th>
              <th>
                Calorie
              </th>
              <th>
                Carb
              </th>
              <th>
                Protein
              </th>
              <th>
                Fat
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="ingredient in recipe.ingredients">
              <td>{{ingredient.Item}}</td>
              <td>{{ingredient.Quantity}}</td>
              <td>{{ingredient.Calories}}</td>
              <td>{{ingredient.Carbohydrate}}</td>
              <td>{{ingredient.Protein}}</td>
              <td>{{ingredient.Fat}}</td>
            </tr>
          </tbody>
        </table>
        <hr v-if="recipe.ingredients.length > 0">
        <h2 v-if="recipe.ingredients.length > 0">{{caloriesPerServing | roundOff}} kcal per serving for <input id="servings" type="number" v-model="recipe.servings"> servings</h2>

        <hr v-if="recipe.ingredients.length > 0">
        <h3>Total Nutrition</h3>
        <table class="table table-striped">
          <tbody>
            <tr>
              <td>Calories</td>
              <td>{{recipe.nutrition.calories | roundOff}}</td>
            </tr>
            <tr>
              <td>Carbohydrate</td>
              <td>{{recipe.nutrition.carbohydrate | roundOff}}</td>
            </tr>
            <tr>
              <td>Protein</td>
              <td>{{recipe.nutrition.protein | roundOff}}</td>
            </tr>
            <tr>
              <td>Fat</td>
              <td>{{recipe.nutrition.fat | roundOff}}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="panel panel-default hide-in-print">
      <div class="panel-heading">
        <h3>Add new Ingredient</h3>
      </div>
      <div class="panel-body hide-in-print">
        <form id="addIngredient" v-on:submit.prevent="addIngredient">
          <div class="form-group">
            <label for="ingredientName">Name</label>
            <input type="text" id="ingredientName" class="form-control" v-model="newIngredient.Item"> 
          </div>
          <div class="form-group">
            <label for="ingredientCalories">Calories</label>
            <input type="text" id="ingredientCalories" class="form-control" v-model="newIngredient.Calories"> 
          </div>
          <div class="form-group">
            <label for="ingredientCarbohydrate">Carbohydrate</label>
            <input type="text" id="ingredientCarbohydrate" class="form-control" v-model="newIngredient.Carbohydrate"> 
          </div>
          <div class="form-group">
            <label for="ingredientProtein">Protein</label>
            <input type="text" id="ingredientProtein" class="form-control" v-model="newIngredient.Protein"> 
          </div>
          <div class="form-group">
            <label for="ingredientFat">Fat</label>
            <input type="text" id="ingredientFat" class="form-control" v-model="newIngredient.Fat"> 
          </div>
          <input type="submit" class="btn btn-primary" value="Add Ingredient">
        </form>
      </div>
    </div>
    <div class="panel panel-default hide-in-print">
      <div class="panel-heading">
        <h3>Ingredient list</h3>
      </div>
      <div class="panel-body">
        <table class="table table-striped">
          <thead>
            <tr>
              <th>
                Item
              </th>
              <th>
                Calorie
              </th>
              <th>
                Carb
              </th>
              <th>
                Protein
              </th>
              <th>
                Fat
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="ingredient in ingredients">
              <td>{{ingredient.Item}}</td>
              <td>{{ingredient.Calories}}</td>
              <td>{{ingredient.Carbohydrate}}</td>
              <td>{{ingredient.Protein}}</td>
              <td>{{ingredient.Fat}}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
  import Hello from './components/Hello'
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
  
  let app = Firebase.initializeApp(config);
  let db = app.database();
  let ingredientsRef = db.ref('ingredients');
  let typeaheadSource;

  ingredientsRef.once('value', snap => {
    typeaheadSource = Object.keys(snap.val());
  });

  let recipe = {
    ingredients: [],
    nutrition: {
      calories: 0,
      carbohydrate: 0,
      protein: 0,
      fat: 0  
    },
    servings: 4
  };

export default {
  name: 'app',
  firebase: {
    ingredients: ingredientsRef
  },
  data () {
    return {
      newIngredient: {
        Item: '',
        Calories: '',
        Carbohydrate: '',
        Protein: '',
        Fat: '',
        Quantity: ''
      },
      recipe: recipe
    }
  },
  filters: {
    roundOff: function(number) {
      return parseInt(number, 10).toFixed(2).replace(/[.,]00$/, "");
    }
  },
  computed: {
    caloriesPerServing: function() {
      return this.recipe.nutrition.calories / this.recipe.servings
    }
  },
  methods: {
    addIngredient: function() {
      ingredientsRef.push(this.newIngredient);
      this.newIngredient.Item = '';
      this.newIngredient.Calories = '';
      this.newIngredient.Carbohydrate = '';
      this.newIngredient.Protein = '';
      this.newIngredient.Fat = '';
      
    },
    addToRecipe: function() {
      const query = ingredientsRef.child(this.newIngredient.Item.trim());
      console.log(this.newIngredient.Item.trim());
      query.once('value', snap => {
        let calorieMap = snap.val();
        console.log(calorieMap);
        for (let prop in calorieMap) {
          if (prop !== 'Item') {
            calorieMap[prop] = parseInt(calorieMap[prop], 10) * this.newIngredient.Quantity / 100;
          }
        }
        calorieMap.Quantity = this.newIngredient.Quantity;

        //reset form
        this.newIngredient.Item = '';
        this.newIngredient.Quantity = '';
        $('#recipeIngredient').focus();
        recipe.ingredients.push( calorieMap );
        recipe.nutrition.calories += calorieMap.Calories;
        recipe.nutrition.carbohydrate += calorieMap.Carbohydrate;
        recipe.nutrition.protein += calorieMap.Protein;
        recipe.nutrition.fat += calorieMap.Fat;
      });
    },
    enableAutocomplete: function(event) {
      $(event.currentTarget).typeahead({
        source: typeaheadSource,
        minLength: 1
      });
    },
    changeFocus: function(event) {
      $('#quantity').focus();
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
input#servings {
  width: 60px;
}
@media print {
  .hide-in-print {
     display: none;
   }
}
</style>
