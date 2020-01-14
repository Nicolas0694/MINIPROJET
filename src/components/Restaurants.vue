<template>
  <div>

    <div>
  <p>
    Rechercher par nom:
    <input class="form-control-lg" type="text" v-model="nomRecherche" v-on:input="getDataFromServer()" />
  </p>
  <p>
    Nombre de restaurants par page :
    <input
      type="range"
      min="2"
      max="100"
      value="10"
      v-on:input="getDataFromServer()"
      v-model="pagesize"
    />
    {{pagesize}}
  </p>
  <h1>Nombre de restaurants : {{nbRestaurants}}</h1>
  <button v-on:click="pagePrecedente()" v-bind:disabled="page==0">Précédent</button>
  <button v-on:click="pageSuivante()" :disabled="page == nbPagesDeResultats">Suivant</button>
  </div>
  <div class="row">
    <div class= "col-md-3">
       <div id="todo-list-example" class="container">
         <div class="row">
             <div class="col-md-12 max auto">
                 <h1 class="text-center">Liste Des restaurants</h1>

                  <form v-on:submit.prevent="addNewRestaurant">
                    <label for="restaurantboroughrinput">Quartier</label>
                      <input type="text" placeholder="Entrer le Quartier" v-model="restaurantborough" 
                      class="form-control" id="restaurantquartierinput"/>
                     
                      <label for="restaurantcuisineinput">Cuisine </label>
                      <input type="text" placeholder="Entrer La Cuisine..." v-model="restaurantcuisine" 
                      class="form-control" id="restaurantcuisineinput"/>

                       <label for="restaurantnameinput">Nom :</label>
                      <input type="text" placeholder="Entrer le Nom..." v-model="restaurantname"
                      class="form-control" id="restaurantnameinput" />
                      
                       <button v-if="this.isEdit==false" type="submit" class="btn btn-success btn-block mt-3" btn-block>Creer</button>
                       <button v-else v-on:click="editRestaurant()" class="btn btn-success btn-block mt-3">Modifier</button>
                  </form>
            </div>
     
         </div>
     
       </div>
   </div>
  <div class="col-md-9">
    <h1>Les Restaurants</h1>
    <table  class="table">
      <thead>
        <tr>
            <th> <i class="info user icon"></i>Nom</th>
            <th> <i class="info user icon"></i>Cuisine</th>
            <th> <i class="info user icon"></i>Quartier</th>
            <th> <i class="lock open icon"></i></th>
            <th> <i class="edit icon"></i></th>
            <th> <i class="trash icon"></i></th>
        </tr>
      </thead>
      <tr v-for="(restaurant, i) in restaurants" :key="i">
        <td class="text-left">{{ restaurant.name }}</td>
        <td class="text-left">{{ restaurant.cuisine }}</td>
        <td class="text-left">{{ restaurant.borough }}</td>

        <td class="text-left">
           <button v-on:click="updateRestaurant(restaurant.borough, restaurant.cuisine, restaurant.name, restaurant._id)" class="btn btn-success">Modifier</button>
           <button v-on:click="deleteRestaurant(restaurant.id)"  class="btn btn-danger">Supprimer</button>
        </td>
      </tr>
    </table>
  </div>
  </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  
  data(){
    return{
      restaurants:[],
      nbRestaurants: 0,
      id:'',
      restaurantname:'',
      restaurantcuisine:'',
      restaurantborough:'',
       page: 0,
      pagesize: 10,
      nomRecherche: "",
      nbPagesDeResultats: 0,
      apiURL: "http://localhost:8080/api/restaurants",
      isEdit:false
    }
  },
  mounted(){
    this.getRestaurants()
  },
  methods:{
    getRestaurants(){
      // ici on fait un fetch pour récupérer des
      // restaurants sur le serveur.
      let url =
        this.apiURL +
        "?page=" +
        this.page +
        "&pagesize=" +
        this.pagesize +
        "&name=" +
        this.nomRecherche;

      fetch(url)
        .then(reponseJSON => {
          return reponseJSON.json();
        })
        .then(reponseJS => {
          // ici on a la réponse sous la forme
          // d'un objet JS
          this.restaurants = reponseJS.data;
          this.nbRestaurants = reponseJS.count;
          this.nbPagesDeResultats = Math.floor(
          this.nbRestaurants / this.pagesize
          );
        });
    },
    addNewRestaurant(){
      axios.post('/api/restaurant',{name:this.restaurantname, cuisine:this.restaurantcuisine, borough:this.restaurantquartier})
     .then((res)=>{
       this.restaurantname=''
       this.restaurantcuisine=''
       this.restaurantborough=''
       this.getRestaurants()
       console.log(res)
     })
  },
  updateRestaurant(borough, cuisine, name, id){
       this.id=id
       this.restaurantborough= borough
       this.restaurantcuisine= cuisine
       this.restaurantname= name
       this.isEdit=true
  },
  editRestaurant(){
    axios.put('/api/restaurant/$(this.id)',{borough:this.restaurantborough, cuisine:this.restaurantcuisine, name:this.restaurantname})
    .then((res)=>{
      this.restaurantborough=''
       this.restaurantcuisine=''
       this.restaurantname=''
       this.isEdit=false
       this.getRestaurants()
       console.log(res)
     }).catch((err)=>{
       console.log(err)
     })
  },
  deleteRestaurant(index){
    this.restaurants.splice(index, 1);
  },
      getColor(index) {
      return index % 2 ? "lightBlue" : "pink";
    },
    pageSuivante() {
      console.log("Page suivante");
      this.page++;
      this.getDataFromServer();
    },
    pagePrecedente() {
      console.log("Page precedente");
      this.page--;
      this.getDataFromServer();
    }
  }
}
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
