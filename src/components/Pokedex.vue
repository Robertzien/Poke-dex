<template>
  <div class="pokedex">
    <div v-if="loading">LOADING</div>
    <div class="pokemon" v-for="pokemon in pokemon">
      <div class="pokemon__name">
        {{ pokemon.name }}
      </div>
      <div class="pokemon__id">
        {{ pokemon.id }}
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        pokemon: [],
        error: null,
        loading: false,
      }
    },
    created() {
      this.fetchPokemon()
    },
    methods: {
      fetchPokemon() {
        this.loading = true;
        this.error = null;
        fetch('https://pokeapi.co/api/v2/pokemon?limit=151&offset=0').then(res => res.json()).then(data => {
          this.loading = false;
          this.pokemon = data.results;
          this.getPokemonIds();
        })
        .catch(error => {
          console.error(error);
          this.loading = false;
          this.error = error;
        });
      },
      getPokemonIds() {
        this.pokemon.forEach(pokemon => {
          fetch(pokemon.url).then(res => res.json()).then(data => {
            pokemon.id = data.id;
          })
        })
      }
    }
  }
</script>