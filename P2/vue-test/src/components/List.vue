<style>
#container-fluid {
  height: 100%;
  width: 100%;
  display: block;
  position: relative;
  z-index: 5;
}
#container-fluid::before {
  content: "";
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: url(https://i.pinimg.com/originals/74/0f/5a/740f5a66187b37efe4c3a28199c3a20e.png)
    no-repeat;
  background-attachment: fixed;
  background-position: center;
  opacity: 0.3;
  position: absolute;
  z-index: -1;
}
#mapGOT {
  height: 100%;
  width: 100%;
  display: block;
  position: relative;
  z-index: 5;
}
#mapGOT::before {
  content: "";
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: url("/src/assets/map.png") no-repeat center center;
  background-color: rgb(238, 214, 183);
  background-attachment: fixed;
  background-size: cover;
  opacity: 0.45;
  position: absolute;
  z-index: -1;
}

.imgv {
  margin-left: 5px;
}
</style>

<template>
  <div>
    <h1 v-show="isLoading">Cargando personajes ...</h1>
    <div id="container-fluid">
      <div class="row">
        <table v-show="!isLoading" class="table table-striped table-inverse table-responsive">
          <tr>
            <th>Nombre</th>
            <th>Casa</th>
            <th>Detalle</th>
          </tr>
          <tr v-for="character in characters" :key="character.id">
            <td>{{ character.name }}</td>
            <td>{{ character.house }}</td>
            <td>
              <button class="btn btn-info" @click="goToDetail(character._id)">Ver detalle</button>
            </td>
          </tr>
        </table>
      </div>
      <div class="row">
        <b-modal ref="myModal" size="lg" title="Details Charater">
          <div id="mapGOT">
            <b-row class="text-center">
              <b-col>House : {{details.house}}</b-col>
            </b-row>
            <hr>
            <b-row class="text-right">
              <b-col>Year of Birth : {{ details.dateOfBirth }}</b-col>
              <b-col>Year of Dead : {{ details.dateOfDeath }}</b-col>
            </b-row>
            <hr>
            <b-row>
              <b-col class="text-center">
                <b-img src="./src/assets/no-imagen.png" fluid v-show="show" class="imgv"></b-img>
                <b-img :src="picture" fluid v-show="!show" class="imgv"></b-img>
                <p class="text-right">Rank: {{details.pageRank}}</p>
              </b-col>
              <b-col>
                <p>Name : {{details.name}}</p>
                <p>Culture : {{details.culture}}</p>
                <p>Heir : {{details.heir}}</p>
              </b-col>
              <b-col>
                <p>Books:</p>
                <ul v-for="book in details.books  " :key="book.id">
                  <li>{{book}}</li>
                </ul>
              </b-col>
              <b-col>
                <p>Titles:</p>
                <ul v-for="title in details.titles  " :key="title.id">
                  <li>{{title}}</li>
                </ul>
              </b-col>
            </b-row>
            <hr>
            <b-row>
              <b-col class="text-right">Parents</b-col>
              <b-col>Mother : {{details.mother}}</b-col>
              <b-col>Father : {{details.father}}</b-col>
            </b-row>
            <br>
          </div>
        </b-modal>
      </div>
    </div>
  </div>
</template>

<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/js/bootstrap.min.js" integrity="sha384-B0UglyR+jN6CkvvICOB2joaf5I4l3gm9GU6Hc1og6Ls7i6U/mkkaduKaBhlAXv9k" crossorigin="anonymous"></script>
<script>
import { listsAllCharacters } from "../services/got.service.js";
import { getACharacter } from "../services/got.service.js";

export default {
  name: "list-component",
  /**
   * @description the data block represents all the local variable of this component.
   */
  data() {
    return {
      characters: [],
      details: [],
      isLoading: false,
      show: true,
      showPicture: false,
      picture: null
    };
  },

  /**
   * @description the create function is the first one to be execute when the component is being created (see vue js lifecycle).
   */
  created() {
    this.isLoading = true;
    listsAllCharacters().then(res => {
      this.characters = res;
      this.isLoading = false;
    });
  },

  /**
   * @description the methods block represents all the local methods of this component.
   */
  methods: {
    /**
     * @description get the detail of a character from the GoT API.
     * @param {string} id. the "_id" of the character that we are going to request.
     * @method goToDetail
     */
    goToDetail(id) {
      this.details = [];
      this.getID = id;
      getACharacter(id).then(res => {
        if (res.data.imageLink != undefined) {
          this.picture = "https://api.got.show" + res.data.imageLink;
          this.show = false;
          this.showPicture = true;
        } else {
          this.show = true;
          this.showPicture = false;
        }
        this.details = res.data;
        this.$refs.myModal.show();
      });
    }
  }
};
</script>
<!--style>
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td,
th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}
</style-->