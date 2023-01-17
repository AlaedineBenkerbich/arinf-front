<template>
  <h1>Pokédex</h1>
  <ul>
    <li v-for="pokemon in pokemonList" :key=pokemon.id>
      <img v-bind:src="getPokemonImgUrl(pokemon)" v-bind:alt="pokemon.name">
      <p>No.000{{ pokemon.id }}</p>
      <p><strong>{{ pokemon.name }}</strong></p>
      <button class="delete-pokemon-button" v-on:click="deletePokemon(pokemon)">
        <img alt="Add to cart" src="../assets/trashcan.svg">
      </button>
    </li>
    <li>
      <button class="add-pokemon-button">
        <img src="../assets/add.png" alt="Add pokemon">
      </button>
    </li>
  </ul>
</template>

<script>
export default {
  methods: {
    getPokemonList() {
      fetch("http://127.0.0.1:8000/pokemon", {
        "method": "GET",
        "headers": {
          "accept": "application/json"
        }
      })
          .then(response => {
            if(response.ok){
              return response.json()
            }
          })
          .then(response => {
            this.pokemonList = response;
          })
          .catch(err => {
            console.log(err);
          });
    },
    deletePokemon(pokemon) {
      fetch("http://127.0.0.1:8000/pokemon/" + pokemon.id, {
        "method": "DELETE",
        "headers": {
          "accept": "*/*"
        }
      })
          .then(response => {
            if(response.ok){
              this.getPokemonList()
            }
          })
          .catch(err => {
            console.log(err);
          });
    },
    // TODO
    getPokemonImgUrl(pokemon) {
      return "https://assets.pokemon.com/assets/cms2/img/pokedex/full/00" + pokemon.id + ".png"
    }
  },
  beforeMount() {
    this.getPokemonList()
  },
  data() {
    return {
      pokemonList: []
    };
  }
}
</script>

<style scoped>
h1 {
  font-size: 46px;
  display: flex;
  justify-content: center;
  font-family: 'Pokemon Solid', sans-serif;
  color: gray;
}

ul {
  padding: 20px 100px;
  list-style-type: none;
  display: grid;
  gap: 1ch;
  grid-template-columns: repeat(4, 1fr);
}

li {
  border: var(--card-border);
  border-radius: 5px;
  transition: scale 100ms ease-in-out;
  position: relative;
}

li:hover,
li:focus-visible {
  scale: 1.03;
  z-index: 1;
}

img {
  object-fit: contain;
  width: 100%;
}

p {
  padding: 0 20px;
}

strong {
  font-size: 24px;
}

.delete-pokemon-button {
  position: absolute;
  z-index: 1;
  top: 8px;
  right: 8px;
  width: 64px;
  height: 64px;
  background: #d20000 none;
  border: none;
  border-radius: 50%;
}

.delete-pokemon-button:hover {
  background: red none;
}

.delete-pokemon-button img {
  width: 32px;
  height: 32px;
}

.add-pokemon-button {
  display: block;
  width: 100%;
  height: 100%;
  background: none;
  border: none;
}

.add-pokemon-button img {
  display: block;
  margin: auto;
  width: 60%;
}
</style>
