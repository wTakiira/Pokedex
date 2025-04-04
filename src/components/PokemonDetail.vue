<template>
  <div class="detail">
    <div class="detail-view card" v-if="show" ref="detailView" @keydown="handleKeyDown" tabindex="0">
      <div v-if="pokemon" class="image">
        <img :src="imageUrl + pokemon.id + '.png'" alt="">
      </div>
      <button class="close" @click="closeDetail">Close</button>
      <div v-if="pokemon" class="data card-body">
        <h2 class="card-title">{{ pokemon.name }}</h2>
        <div class="property">
          <div class="left font">Expérience de Base</div>
          <div class="right">{{ pokemon.base_experience }} XP</div>
        </div>
        <div class="property">
          <div class="left font">Taille</div>
          <div class="right">{{ pokemon.height / 10 }} m</div>
        </div>
        <div class="property">
          <div class="left font">Poids</div>
          <div class="right">{{ pokemon.weight / 10 }} kg</div>
        </div>
        <h3>Types</h3>
        <div class="types">
          <div class="type" 
            v-for="(value, index) in pokemon.types"
            :key="'value'+index"><span :class="value.type.name">
            {{ value.type.name }}
            </span>
          </div>
        </div>
        <h3>Capacité</h3>
        <div class="abilities">
          <div class="ability" 
            v-for="(value, index) in pokemon.abilities"
            :key="'value'+index">
            {{ value.ability.name }}
          </div>
          <div class="right inactive">{{pokemon.moves.map(item => ' ' + item.move.name).toString()}} .</div>
        </div>        
      </div>
      <h2 v-else>Pokemon non trouvé</h2>
    </div>
    <i v-else class="fas fa-spinner fa-spin"></i>
  </div>
</template>

<script>
export default {
  props: [
    'pokemonUrl',
    'imageUrl'
  ],
  data() {
    return {
      show: false,
      pokemon: {}
    };
  },
  methods: {
    fetchData() {
      let req = new Request(this.pokemonUrl);
      fetch(req)
        .then((resp) => {
          if (resp.status === 200) return resp.json();
        })
        .then((data) => {
          this.pokemon = data;
          this.show = true;
          window.history.pushState(null, null, `?pokemon=${this.pokemon.id}`);
          
          // Appliquer le focus sur le détail une fois qu'il est visible
          this.$nextTick(() => {
            if (this.$refs.detailView) {
              this.$refs.detailView.focus();
            }
          });
        })
        .catch((error) => {
          console.log(error);
        });
    },
    closeDetail() {
      this.$emit('closeDetail');
    },
    handleKeyDown(event) {
      if (event.key === 'Escape' || event.key === 'Enter') {
        this.closeDetail(); // Fermer le détail si Echap ou Entrée est pressé
      }
    }
  },
  created() {
    this.fetchData();
  }
};
</script>

<style lang="scss" scoped>
.detail-view {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  width: 90%;
  padding: 50px 0 0;
  position: relative;
  max-width: 510px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 15px 30px rgba(0,0,0,.2), 0 10px 10px rgba(0,0,0,.2);
  outline: none;
  /* Ajouté pour le focus */
}

.type {
            .grass {background:rgb(3, 139, 44) !important;}
            .poison {background:rgb(74, 7, 105) !important;}
            .water {background:rgb(8, 135, 219) !important;}
            .dragon {background:rgb(27, 2, 68) !important;}
            .ice {background:rgb(78, 199, 255) !important;}
            .flying {background:rgb(145, 215, 255) !important;}
            .fire {background:rgb(238, 135, 17) !important;}
            .ghost {background:rgb(74, 52, 87) !important;}
            .fighting {background:rgb(122, 0, 0) !important;}
            .normal {background:rgb(104, 104, 104) !important;}
            .psychic {background:rgb(195, 0, 255) !important;}
            .bug {background:rgb(52, 87, 6) !important;}
            .dark {background:rgb(43, 43, 43) !important;}
            .steel {background:rgb(116, 116, 116) !important;}
            .fairy {background:rgb(248, 165, 237) !important;}
            .eletric {background:rgb(255, 217, 1) !important;}
            .rock {background:rgb(88, 95, 100) !important;}
            .ground {background:rgb(92, 70, 70) !important;}
        }
        

.detail-view:focus {
  outline: none;
}

i.fa-spinner {
  text-align: center;
}

.detail {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  position: fixed;
  top: 0;
  left: 0;
  padding: 90px 10px 10px;
  width: 100%;
  height: 100vh;
  background: rgba(10, 7, 0, 0.562);
}

.image {
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  top: -60px;
  width: 120px;
  height: 120px;
  background-color: #b9b1b1;
  border-radius: 50%;
  overflow: hidden;
  box-shadow: 0 15px 30px rgba(0,0,0,.2), 0 10px 10px rgba(0,0,0,.2);
}

h2 {
  text-transform: capitalize;
  padding: 10px;
}

.data {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  flex-direction: column;
  width: 100%;
  margin-bottom: 40px;
}

.property {
  width: 90%;
  max-width: 400px;
  border-bottom: 1px solid #ccc;
  margin-bottom: 10px;
}

.left {
  float: left;
}

.right {
  float: right;
}

h3 {
  width: 90%;
  max-width: 400px;
  border-bottom: 1px solid #ccc;
  font-weight: bold;
  font-size: 16px;
}

.types,
.abilities {
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
  width: 90%;
  max-width: 400px;
}

.type {
  margin: 0 0 10px 0;
  padding: 25px 10px 10px;
  font-weight: bold;
  font-size: 1rem;
  letter-spacing: 2px;
  text-transform: capitalize;
}

.type span {
  color: #ffffff !important;
  padding: 10px 14px;
  border-radius: 29px;
}

.ability {
  color: rgb(10, 119, 10);
  margin: 10px 10px 10px 0;
  border-radius: 20px;
  padding: 5px 10px;
  font-weight: bold;
  font-size: 1rem;
  letter-spacing: 2px;
  text-transform: capitalize;
  word-wrap: none;
  word-break: keep-all;
  background-color: #ffffff;
  border: 3px solid;
}

.close {
  position: absolute;
  top: 10px;
  left: 10px;
  z-index: 10;
  display: block;
  outline: none;
  border: none;
  border-radius: 5px;
  background-color: #C73015;
  color: #efefef;
  padding: 10px 20px;
  font-size: 1.2rem;
  cursor: pointer;
}

i {
  font-size: 2rem;
  color: #efefef;
}

.font {
  font-weight: bold;
}

.inactive {
  display: none;
}
</style>
