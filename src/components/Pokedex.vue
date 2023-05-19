<template>
  <Loading v-if="loading"/>
  <div class="pokedex" v-if="!loading">
    <div class="pokedex__filters">
      <div class="pokedex__filter next-gen">
        <div class="pokedex__filters-heading">
          Next gen
        </div>
<!--        Voeg een icoontje toe aan de knop want het is niet echt duidelijk dat het een knop is-->
        <button class="button" @click="showNextGen()"></button>
      </div>
      <div class="pokedex__filter range">
        <div class="pokedex__filters-heading">
          Range
        </div>
        <div class="range__inputs">
          <div class="range__start">
            <input type="number" v-model="startRange">
          </div>
          <div class="range__dash">-</div>
          <div class="range__end">
            <input type="number" v-model="endRange">
          </div>
        </div>
      </div>
    </div>
    <div class="pokedex__cards">
      <div class="card" v-for="pokemon in displayedPokemon">
        <div class="card__image">
          <picture>
            <img :src="pokemon.spriteUrl" alt="">
          </picture>
        </div>
        <div class="card__name">
          {{ pokemon.name }}
        </div>
        <div class="card__id">
          N&#176;{{ pokemon.id }}
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import Loading from "./Loading.vue";

export default {
  data() {
    return {
      pokemon: [],
      error: null,
      loading: false,
      totalPokemon: 0,
      pokemonCompleted: 0,
      startRange: 1,
      endRange: 151,
      currentGen: 1,
    };
  },
  computed: {
    displayedPokemon() {
      return this.pokemon.filter((pokemon) => {
        return pokemon.id >= this.startRange && pokemon.id <= this.endRange;
      });
    },
  },
  components: {
    Loading,
  },
  created() {
    this.fetchFirstGen();
  },
  methods: {
    //Is er misschien een manier om dit korter te maken?
    showNextGen() {
      if (this.currentGen === 1) {
        this.fetchSecondGen();
        this.currentGen++;
      } else if (this.currentGen === 2) {
        this.fetchThirdGen();
        this.currentGen++;
      } else if (this.currentGen === 3){
        this.fetchFourthGen();
        this.currentGen++;
      } else {
        this.fetchFirstGen();
        this.currentGen = 1;
        this.startRange = 1;
        this.endRange = 151;
      }
    },
    fetchFirstGen() {
      this.loading = true;
      this.error = null;
      fetch("https://pokeapi.co/api/v2/pokemon?limit=151&offset=0")
          .then((res) => res.json())
          .then((data) => {
            this.pokemon = data.results;
            this.totalPokemon = this.pokemon.length;
            this.getPokemonIds();
            this.toUpperCase();
          })
          .catch((error) => {
            console.error(error);
            this.loading = false;
            this.error = error;
          });
    },
    fetchSecondGen() {
      this.loading = true;
      this.error = null;
      this.startRange = 152;
      this.endRange = 251;
      fetch("https://pokeapi.co/api/v2/pokemon?limit=251&offset=151")
          .then((res) => res.json())
          .then((data) => {
            this.pokemon = data.results;
            this.totalPokemon = this.pokemon.length;
            this.toUpperCase();
            this.getPokemonIds();
          })
          .catch((error) => {
            console.error(error);
            this.loading = false;
            this.error = error;
          });
    },
    fetchThirdGen() {
      this.loading = true;
      this.error = null;
      this.startRange = 252;
      this.endRange = 386;
      fetch("https://pokeapi.co/api/v2/pokemon?limit=386&offset=251")
          .then((res) => res.json())
          .then((data) => {
            this.pokemon = data.results;
            this.totalPokemon = this.pokemon.length;
            this.toUpperCase();
            this.getPokemonIds();
          })
          .catch((error) => {
            console.error(error);
            this.loading = false;
            this.error = error;
          });
    },
    fetchFourthGen() {
      this.loading = true;
      this.error = null;
      this.startRange = 386;
      this.endRange = 493;
      fetch("https://pokeapi.co/api/v2/pokemon?limit=493&offset=386")
          .then((res) => res.json())
          .then((data) => {
            this.pokemon = data.results;
            this.totalPokemon = this.pokemon.length;
            this.toUpperCase();
            this.getPokemonIds();
          })
          .catch((error) => {
            console.error(error);
            this.loading = false;
            this.error = error;
          });
    },
    async getPokemonIds() {
      const urls = this.pokemon.map(pokemon => pokemon.url);
      const responses = await Promise.all(urls.map(url => fetch(url)));
      const data = await Promise.all(responses.map(res => res.json()));
      this.pokemon.forEach((pokemon, i) => {
        pokemon.id = data[i].id;
        pokemon.spriteUrl = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${data[i].id}.png`;
      });
      this.loading = false;
    },
    toUpperCase() {
    this.pokemon.map((pokemon) => {
        pokemon.name = pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
        return pokemon;
      });
    },
  },
};
</script>