

<script>
import axios from 'axios';

export default {
  data() {
    return {
      pokemonName: '',
      pokemonDetails: {
        name: '',
        type: '',
        weight: '',
        height: '',
        stats: '',
        sprites: ''
      },
      caughtPokemons: []
    };
  },
  methods: {
    searchPokemon() {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${this.pokemonName.toLowerCase()}`)
        .then(response => {
          // Gestisci la risposta dell'API
          this.pokemonDetails = {
            name: response.data.name,
            type: response.data.types[0].type.name,
            weight: response.data.weight,
            height: response.data.height,
            stats: response.data.stats,
            sprites: response.data.sprites,
          };
        })
        .catch(error => {
          // Gestisci gli errori
          console.error('Errore durante la chiamata API:', error);
        });
    },
    catchPokemon() {
      // Controlla se il Pokémon è già presente nella lista
      const isPokemonCaught = this.caughtPokemons.some(pokemon => pokemon.name === this.pokemonDetails.name);
      if (!isPokemonCaught) {
        // Aggiungi il Pokémon alla lista solo se non è già presente
        this.caughtPokemons.push(this.pokemonDetails);
        // Salva la lista aggiornata dei Pokémon catturati in localStorage
        localStorage.setItem('caughtPokemons', JSON.stringify(this.caughtPokemons));
      }
    }
  },

  created() {
    const caughtPokemons = JSON.parse(localStorage.getItem('caughtPokemons')) || [];
    this.caughtPokemons = caughtPokemons;
  }
};
</script>

<template>
  <div class="container mx-auto">
    <div class="row my-5 ">
      <!-- buttons e descrizione pokemon -->
      <div class="col border border-black p-3">
        <div class="row justify-content-between">
          <div class="col">
            <!-- search bar -->
            <div class="input-group mb-3">
              <input type="text" class="form-control" v-model="pokemonName" placeholder="Search Pokemon..."
                aria-label="Recipient's username" aria-describedby="button-addon2">
              <button class="btn btn-outline-secondary" type="button" @click="searchPokemon" id="button-addon2"><i
                  class="fa-solid fa-magnifying-glass"></i></button>
            </div>
          </div>
          <!-- button cattura -->
          <div class="col-lg-3">
            <button v-if="pokemonDetails.name" type="button" class="btn btn-danger" @click="catchPokemon">Catch
              it!</button>

          </div>
        </div>

        <!-- contenitore pokemon -->
        <div class="border border-warning text-center my-3">
          <img :src="pokemonDetails.sprites.front_default" :alt="pokemonDetails.name">
          <img :src="pokemonDetails.sprites.back_default" :alt="pokemonDetails.name">

        </div>
        <!-- scheda tecnica -->
        <div>

          <ul class="list-unstyled">
            <li><strong>Name:</strong> {{ pokemonDetails.name }}</li>
            <li><strong>Type:</strong> {{ pokemonDetails.type }}</li>
            <li><strong>Weight:</strong> {{ pokemonDetails.weight }} lbs</li>
            <li><strong>Height:</strong> {{ pokemonDetails.height }} ''</li>
          </ul>

          <br>

          <h5>Stats</h5>
          <ul class="list-unstyled">
            <li v-for="(   stat, index   ) in    pokemonDetails.stats   " :key="index">
              <span>{{ stat.stat.name }}</span>
              <div class="progress">
                <div class="progress-bar bg-success" role="progressbar" :style="{ width: stat.base_stat + '%' }"></div>
              </div>
            </li>
          </ul>


        </div>
      </div>
      <!-- lista pokemon catturati -->
      <div class="col border border-start-0 border-black px-5 py-3 text-center">
        <h2>I miei Pokemon</h2>
        <div class="row row-cols-5 text-center pt-4">
          <span class="col border border-black-subtle" v-for="pokemon in caughtPokemons" :key="pokemon.name">
            <img :src="pokemon.sprites.front_default" alt="">
            <span class="bg-secondary-subtle">{{ pokemon.name }}</span>

          </span>
        </div>
      </div>
    </div>
  </div>
</template>



<style></style>
