<template>
  <div class="list">
    <button v-for="(pokemon, index) in filteredPokemons"
      :key="'poke' + index"
      @click="setPokemonUrl(pokemon.url)">
      <img :src="imageUrl + pokemon.id + '.png'" width="160" height="160" alt="">
      <h3>{{ pokemon.name }}</h3>
  </button>
    <div id="scroll-trigger" ref="infinitescrolltrigger">
      <i class="fas fa-spinner fa-spin"></i>
    </div>
  </div>
</template>

<script>
export default {
  props: [
    "imageUrl",
    "apiUrl",
    "selectedType",
    "selectedGen"
  ],
  data() {
    return {
      pokemons: [],
      filteredPokemons: [],
      nextUrl: "",
      currentUrl: ""
    };
  },
  watch: {
    selectedType() {
      this.applyFilters();
    },
    selectedGen() {
      this.applyFilters();
    }
  },
  methods: {
    async fetchData() {
  let req = new Request(this.currentUrl);
  fetch(req)
    .then((resp) => resp.json())
    .then(async (data) => {
      this.nextUrl = data.next;
      const newPokemons = data.results.map(pokemon => {
        return {
          ...pokemon,
          id: pokemon.url.split('/').filter(part => !!part).pop()
        };
      });

      this.pokemons = [...this.pokemons, ...newPokemons];
      await this.applyFilters();
    })
    .catch((error) => console.log(error));
},

next() {
  if (this.nextUrl) {
    this.currentUrl = this.nextUrl;
    this.fetchData();
  }
},

    async applyFilters() {
      let filteredList = [...this.pokemons];

      // Filtrage par Type
      if (this.selectedType) {
        const typeResponse = await fetch(`https://pokeapi.co/api/v2/type/${this.selectedType}`);
        const typeData = await typeResponse.json();
        const typePokemons = typeData.pokemon.map(p => p.pokemon.name);
        filteredList = filteredList.filter(p => typePokemons.includes(p.name));
      }

      // Filtrage par Génération
      if (this.selectedGen) {
        const genResponse = await fetch(`https://pokeapi.co/api/v2/generation/${this.selectedGen}`);
        const genData = await genResponse.json();
        const genPokemons = genData.pokemon_species.map(p => p.name);
        filteredList = filteredList.filter(p => genPokemons.includes(p.name));
      }

      this.filteredPokemons = filteredList;
    },
    scrollTrigger() {
      const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
          if (entry.intersectionRatio > 0 && this.nextUrl) {
            this.next();
          }
        });
      });

      observer.observe(this.$refs.infinitescrolltrigger);
    },
    next() {
      this.currentUrl = this.nextUrl;
      this.fetchData();
    },
    setPokemonUrl(url) {
      this.$emit("setPokemonUrl", url);
    }
  },
  created() {
    this.currentUrl = this.apiUrl;
    this.fetchData();
  },
  mounted() {
    this.scrollTrigger();
  }
};
</script>

<style lang="scss" scoped>
.list {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  grid-gap: 10px;
  width: 100%;
  max-width: 510px;
}

button {
  height: 200px;
  background-color: #efefef;
  text-align: center;
  text-transform: capitalize;
  border-radius: 5px;
  cursor: pointer;
  box-shadow: 0 15px 30px rgba(0,0,0,.2), 0 10px 10px rgba(0,0,0,.2);
}

article {
  height: 200px;
  background-color: #efefef;
  text-align: center;
  text-transform: capitalize;
  border-radius: 5px;
  cursor: pointer;
  box-shadow: 0 15px 30px rgba(0,0,0,.2), 0 10px 10px rgba(0,0,0,.2);
}

h3 {
  margin: 0;
  padding-bottom: 20px;
}

#scroll-trigger {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 150px;
  font-size: 2rem;
  color: #efefef;
}
</style>
