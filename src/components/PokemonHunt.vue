<template>
    <div class="container-pokemon">
      <h2>Pokémon Sauvages</h2>
      <button class="fetch-btn" @click="fetchPokemons">Chercher des Pokémon</button>
  
      <div class="pokemon-list" v-if="wildPokemons.length">
        <div v-for="pokemon in wildPokemons" :key="pokemon.id" class="pokemon-card">
          <img :src="pokemon.image" :alt="pokemon.name" class="pokemon-img" />
          <div class="pokemon-info">
            <h3>{{ pokemon.name }}</h3>
            <button class="capture-btn" @click="capturePokemon(pokemon)">Capturer</button>
          </div>
        </div>
      </div>
  
      <h2>Pokémon Capturés</h2>
      <div class="pokemon-list" v-if="capturedPokemons.length">
        <div v-for="pokemon in capturedPokemons" :key="pokemon.id" class="pokemon-card">
          <img :src="pokemon.image" :alt="pokemon.name" class="pokemon-img" />
          <div class="pokemon-info">
            <h3>{{ pokemon.name }}</h3>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        wildPokemons: [],
        capturedPokemons: [],
      };
    },
    methods: {
      async fetchPokemons() {
        this.wildPokemons = [];
        const promises = [];
        for (let i = 0; i < 5; i++) {
          const randomId = Math.floor(Math.random() * 898) + 1;
          promises.push(fetch(`https://pokeapi.co/api/v2/pokemon/${randomId}`).then(res => res.json()));
        }
        const results = await Promise.all(promises);
        this.wildPokemons = results.map(pokemon => ({
          id: pokemon.id,
          name: pokemon.name,
          image: pokemon.sprites.front_default,
        }));
      },
      capturePokemon(pokemon) {
        const success = Math.random() < 0.3;
        if (success) {
          this.capturedPokemons.push(pokemon);
          this.wildPokemons = [];
        } else {
          this.wildPokemons = [];
          alert(`${pokemon.name} s'est échappé !`);
        }
      },
    },
  };
  </script>
  
  <style>
  .container-pokemon {
    text-align: center;
    padding-bottom: 50px;
  }
  
  .fetch-btn {
    background-color: #ffcc00;
    color: #222;
    padding: 10px 15px;
    border: none;
    border-radius: 8px;
    font-size: 16px;
    cursor: pointer;
    transition: 0.3s;
  }
  
  .fetch-btn:hover {
    background-color: #ffdb4d;
  }
  
  .pokemon-list {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    gap: 20px;
    margin-top: 20px;
  }
  
  .pokemon-card {
    background: #fff;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    width: 180px;
    padding: 15px;
    display: flex;
    flex-direction: column;
    align-items: center;
    transition: 0.3s;
  }
  
  .pokemon-card:hover {
    transform: scale(1.05);
  }
  
  .pokemon-img {
    width: 100px;
    height: 100px;
  }
  
  .pokemon-info {
    text-align: center;
  }
  
  .pokemon-info h3 {
    font-size: 18px;
    margin: 10px 0;
  }
  
  .capture-btn {
    background-color: #ff4444;
    color: white;
    padding: 8px 12px;
    border: none;
    border-radius: 5px;
    font-size: 14px;
    cursor: pointer;
    transition: 0.3s;
  }
  
  .capture-btn:hover {
    background-color: #cc0000;
  }
  </style>
  