<template>
  <div class="container">
    <img
      alt="Vue logo"
      src="https://upload.wikimedia.org/wikipedia/commons/9/98/International_Pok%C3%A9mon_logo.svg"
    >
    <h1>Mon Pokedex de Poche</h1>

    <PokemonHunt/>

    <!-- Ajout du composant de recherche -->
    <PokemonSearch :apiUrl="apiUrl" @setPokemonUrl="setPokemonUrl"/>

    <!-- Filtres ajoutés -->
    <div class="filters">
      <select v-model="selectedType">
        <option value="">Tous les types</option>
        <option v-for="type in types" :key="type.name" :value="type.name">
          {{ type.name }}
        </option>
      </select>

      <select v-model="selectedGen">
        <option value="">Toutes les générations</option>
        <option v-for="gen in generations" :key="gen.name" :value="gen.name">
          {{ gen.name }}
        </option>
      </select>
    </div>

    <!-- Liste des Pokémon avec les filtres -->
    <PokemonList 
      :imageUrl="imageUrl" 
      :apiUrl="apiUrl" 
      :selectedType="selectedType" 
      :selectedGen="selectedGen"
      @setPokemonUrl="setPokemonUrl"
    />

    <!-- Détails du Pokémon -->
    <PokemonDetail
      v-if="showDetail"
      :pokemonUrl="pokemonUrl"
      :imageUrl="imageUrl"
      @closeDetail="closeDetail"
    />
  </div>
</template>

<script>
import PokemonSearch from "./PokemonSearch.vue";
import PokemonList from "./PokemonList.vue";
import PokemonDetail from "./PokemonDetail.vue";
import PokemonHunt from "./PokemonHunt.vue";

export default {
  data() {
    return {
      imageUrl: "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/",
      apiUrl: "https://pokeapi.co/api/v2/pokemon/",
      pokemonUrl: "",
      showDetail: false,
      selectedType: "",
      selectedGen: "",
      types: [],
      generations: []
    };
  },
  components: {
    PokemonSearch,
    PokemonList,
    PokemonDetail,
    PokemonHunt
  },
  methods: {
    setPokemonUrl(url) {
      this.pokemonUrl = url;
      this.showDetail = true;
    },
    closeDetail() {
      this.pokemonUrl = "";
      this.showDetail = false;
    },
    async fetchFilters() {
      const typeResponse = await fetch("https://pokeapi.co/api/v2/type/");
      const genResponse = await fetch("https://pokeapi.co/api/v2/generation/");
      const typeData = await typeResponse.json();
      const genData = await genResponse.json();
      this.types = typeData.results;
      this.generations = genData.results;
    }
  },
  created() {
    this.fetchFilters();
  }
};
</script>

<style lang="scss" scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: 10px;
  width: 100%;
  min-height: calc(100vh - 20px);
  font-family: "Acme", arial;
  font-size: 1rem;
}

h1 {
  color: #efefef;
}

.filters {
  display: flex;
  gap: 10px;
  margin: 20px 0;
}

.filters select {
  padding: 5px;
  font-size: 1rem;
  border-radius: 5px;
}
</style>
