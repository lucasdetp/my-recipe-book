<template>
  <link rel="stylesheet" href="../../public/style.css">
  <div class="main">
  <h1 class="main-title">My Recipe Book</h1>
  <h2 class="main-title">Add Recipe</h2>
  <form class="new-recipe-form" @submit.prevent="addRecipe">
    <input type="text" class="recipe-title-input" placeholder="Add a new recipe ..." v-model="newRecipe.title"/>
    <button type="submit" class="recipe-create-button">Add Recipe</button>
  </form>
  <h2 class="main-title">My Recipes</h2>
<ul class="recipe-book">
  <li class="recipe" v-for="(recipe, index) in recipes" :key="index">
    <h3 class="recipe-title">{{ recipe.title }}</h3>
    <div class="recipe-actions">
      <button class="recipe-edit-button" @click="editRecipe(index)">Edit</button>
      <button class="recipe-delete-button" @click="deleteRecipe(index)">Delete</button>
    </div>
  </li>
</ul>
<form class="recipe-edit-form" @submit.prevent="saveRecipe" v-if="editingRecipe !== null">
  <label for="recipe-title">Title</label>
  <input type="text" id="recipe-title" class="recipe-edit-title-input" v-model="newRecipe.title" />

  <label for="recipe-description">Description</label>
  <textarea id="recipe-description" class="recipe-edit-description-input" v-model="newRecipe.description"></textarea>

  <label for="recipe-ingredients">Ingredients</label>
  <ul class="recipe-ingredients-list">
    <li class="recipe-ingredient" v-for="(ingredient, ingredientIndex) in recipes[editingRecipe].ingredients.split('\n')" :key="ingredientIndex">
      <input type="text" class="recipe-ingredient-input" :value="ingredient" @input="updateIngredient(editingRecipe, ingredientIndex, $event.target.value)" />
      <button type="button" class="recipe-ingredient-delete-button" @click="deleteIngredient(editingRecipe, ingredientIndex)">X</button>
    </li>
    <li class="recipe-new-ingredient">
      <input type="text" class="recipe-new-ingredient-input" placeholder="Add new ingredient" v-model="newIngredient" />
      <button type="button" class="recipe-ingredient-add-button" @click="addIngredient(editingRecipe)">+</button>
    </li>
  </ul>
  <div class="recipe-edit-actions">
    <button type="submit" class="recipe-edit-save-button">Save</button>
    <button type="button" class="recipe-edit-cart-button" @click="addToShoppingList(recipes[editingRecipe])">Add to shopping list</button>
    <button type="button" class="recipe-edit-cancel-button" @click="cancelRecipe">Cancel</button>
  </div>
</form>

  <div className="shopping-list">
  <h2 class="main-title">Shopping List</h2>

  <ul class="recipe-ingredients-list">
    <li class="recipe-ingredient" v-for="(item, index) in shoppingList" :key="index">
    <input type="checkbox" :id="'item-' + index" v-model="item.checked">
    <label :for="'item-' + index" class="shopping-list-item">{{ item.name }}</label>
    </li>
  </ul>

  <div class="shopping-list-actions">
    <button @click="checkAllItems">Check All</button>
    <button @click="deleteCheckedItems">Clear checked items</button>
    <button @click="clearAll">Clear all</button>
  </div>
  
  </div>
</div>
          </template>
          
          <script>
          export default {
            data() {
              return {
                //recette de base 
                recipes: [
                            {
                              title: "Poke bowl saumon avocat",
                              description: "Mélangez le pavé de saumon coupé en dés avec 1 cuil. à soupe de vinaigre de riz, 1 cuil. à soupe de sauce soja et des graines de sésame. Réservez. Mélangez du riz à sushi cuit et refroidi avec 1 cuil. à soupe de vinaigre de riz, 1 cuil. à soupe de sauce soja, des graines de sésame, et 1 cuil. à soupe de ciboulette ciselée. Réservez. Placez le riz au fond d’un bol, ajoutez des graines de sésame, des tranches de concombre, des radis émincés, des tranches d’avocat, le saumon assaisonné, 1 cuil. à soupe de ciboulette ciselée et de la coriandre émincée. Servez.",
                              ingredients: "1 pavé de saumon\n2 cuillère(s) à soupe de vinaigre de riz\n2 cuillère(s) à soupe de sauce soja\ngraines de sésame\nriz à sushi cuit et refroidi"
                            },
                            {
                              title: "Soupe de poissons comme à Marseille",
                              description: "Écaillez les poissons, videz-les, rincez-les et épongez-les. Lavez les tomates et coupez-les en quartiers. Epluchez l'oignon, émincez-le ainsi que le blanc de poireau. Pelez les gousses d'ail et hachez-les grossièrement. Lavez le poivron et coupez-le en fines lanières. Faites chauffer l'huile dans une grande cocotte et faites-y blondir l'oignon, l'ail, le poireau et le poivron. Ajoutez alors les tomates, le fenouil, le thym, le laurier, le persil, le safran et les poissons. Salez et poivrez. Mélangez pendant 2 mn, puis couvrez et laissez cuire 10 mn à feu doux. Versez alors 2 litres d'eau bouillante, couvrez et laissez cuire 20 mn à feu doux. Préparez la rouille : pelez les gousses d'ail et hachez-les grossièrement. Lavez les piments et coupez-les en deux ; ôtez les graines et le pédoncule, hachez grossièrement la pulpe. Mettez-la dans le bol d'un robot avec l'ail, le safran et le gros sel. Mixez jusqu'à obtention d'une pommade. Ajoutez la mie de pain en l'émiettant, puis mixez encore. Versez enfin l'huile en filet, sans cesser de mixer, jusqu'à obtention d'une sauce épaisse et parfumée, couleur rouille. Lorsque la soupe est cuite, passez-la au tamis, en pressant bien pour extraire tout le suc des poissons. Rincez la cocotte et versez-y à nouveau la soupe, en la filtrant. Faites-la réchauffer sur feu doux. Versez la soupe dans une soupière et servez à table aussitôt, avec la rouille à part. Vous en tartinerez les tranches de pain grillé avant de les plonger dans la soupe.",
                              ingredients: "1,5 kg de petits poissons de roche mélangés\n500 g de tomates\n1 gros oignon\n1 blanc de poireau\n1 petit poivron vert\n4 gousses d'ail\n4 brins de fenouil séché\n1 brin de thym séché\n2 feuilles de laurier\n3 tiges de persil\n4 pincées de filaments de safran\n4 cuillère(s) à soupe d'huile d'olive"
                            },
                            {
                              title: "Specialité de Lucas",
                              description:"La meilleure recette étudiante !",
                              ingredients: "1 cordon bleu\nUne poignée de pate (au choix)\nUne cuillère de sauce bolo\nFromage râper"
                            }
                          ],
                newRecipe: {
                  title: "",
                  description: "",
                  ingredients: "",
                },
                editingRecipe: null,
                shoppingList: [],
                newIngredient: "", 
              };
            },
        
            methods: {
              //ajouter une recette 
              addRecipe() {
                if (this.newRecipe.title ) {
                   this.recipes.push({
                  title: this.newRecipe.title,
                  description: this.newRecipe.description,
                  ingredients: this.newRecipe.ingredients,
                });
                this.newRecipe = {
                  title: "",
                  description: "",
                  ingredients: "",
                };
               }
              },
              //bouton suppr une recette 
              deleteRecipe(index) {
                this.recipes.splice(index, 1);
              },
              //bouton edit
              editRecipe(index) {
                this.editingRecipe = index;
                this.newRecipe.title = this.recipes[index].title;
                this.newRecipe.description = this.recipes[index].description;
                this.newRecipe.ingredients = this.recipes[index].ingredients;
              },
              //bouton save
              saveRecipe() {
                const editedRecipe = this.recipes[this.editingRecipe];
                editedRecipe.title = this.newRecipe.title;
                editedRecipe.description = this.newRecipe.description;
                editedRecipe.ingredients = this.newRecipe.ingredients;
                this.newRecipe.description = "";
                this.newRecipe.ingredients = "";
                this.editingRecipe = null;
              },
              //bouton annulé 
              cancelRecipe() {
                this.editingRecipe = null;
                this.newRecipe = {
                  title: "",
                  description: "",
                  ingredients: ""
                };
              },
              //ajout des elements dans la liste de course
              addToShoppingList(recipe) {
                const ingredientsArray = recipe.ingredients.split("\n");
                for (let i = 0; i < ingredientsArray.length; i++) {
                  this.shoppingList.push({
                    name: ingredientsArray[i],
                    checked: false
                  });
                }
              },
              //suppr all liste de course
              clearAll() {
                this.shoppingList = [];
              },
              //check toutes les cases et dé check
              checkAllItems() {
                let allChecked = true;
                for (let i = 0; i < this.shoppingList.length; i++) {
                  if (!this.shoppingList[i].checked) {
                    allChecked = false;
                    break;
                  }
                }
                for (let i = 0; i < this.shoppingList.length; i++) {
                  this.shoppingList[i].checked = !allChecked;
                }
              },
              //suppression de tous les éléments
              deleteIngredient(index, ingredientIndex) {
                const ingredientsArray = this.recipes[index].ingredients.split("\n");
                ingredientsArray.splice(ingredientIndex, 1);
                this.recipes[index].ingredients = ingredientsArray.join("\n");
                this.newRecipe.ingredients = this.recipes[index].ingredients;
              },
              //suppression liste de course check
              deleteCheckedItems() {
                this.shoppingList = this.shoppingList.filter(item => !item.checked);
              },
              
              //ajout ingredient
              addIngredient(index) {
                const ingredient = this.newIngredient.trim();
                if (ingredient !== '') {
                  this.recipes[index].ingredients += (this.recipes[index].ingredients ? '\n' : '') + ingredient;
                  this.newRecipe.ingredients = this.recipes[index].ingredients
                    .split('\n')
                    .filter(ingredient => ingredient.trim() !== '')
                    .join('\n');
                  this.newIngredient = '';
                }
              }
            },
          };
        </script>
 