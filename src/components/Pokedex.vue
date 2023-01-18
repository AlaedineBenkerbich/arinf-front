<template>
  <div @keydown.esc="hideModal">
    <h1>Pokédex</h1>
    <ul>
      <li v-for="pokemon in pokemonList" :key=pokemon.id>
        <button class="item-link" v-on:click="showModalToUpdatePokemon(pokemon)"></button>
        <img v-bind:src="getPokemonImgUrl(pokemon)" v-bind:alt="pokemon.name">
        <p>{{ getPokemonNo(pokemon) }}</p>
        <p><strong>{{ pokemon.name }}</strong></p>
        <button class="delete-pokemon-button" v-on:click="deletePokemon(pokemon)">
          <img alt="Remove pokemon" src="../assets/trashcan.svg">
        </button>
      </li>
      <li>
        <button class="add-pokemon-button" v-on:click="showModal">
          <img src="../assets/add.png" alt="Add pokemon">
        </button>
      </li>
    </ul>

    <!-- The Modal -->
    <div class="modal" v-if="modalVisible">
      <!-- Modal content -->
      <form class="modal-content" v-on:submit="savePokemonForm">
        <fieldset>
          <legend>{{ getModalTitle() }}</legend>
          <input type="text" placeholder="Nom" v-model="pokemonName"/>
        </fieldset>
        <button>
          <img src="../assets/save.png" alt="Enregistrer">
          Enregistrer
        </button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  methods: {
    showModal() {
      this.modalVisible = true;
    },
    hideModal() {
      this.modalVisible = false;
      this.pokemonName = "";
      this.updatingPokemon = false;
    },
    showModalToUpdatePokemon(pokemon) {
      this.showModal();
      this.pokemonName = pokemon.name;
      this.updatingPokemon = true;
      this.pokemonToUpdate = pokemon;
    },
    getPokemonList() {
      fetch(import.meta.env.VITE_BACKEND_HOST + "/pokemon", {
        "method": "GET",
        "headers": {
          "accept": "application/json"
        }
      })
          .then(response => {
            if (response.ok) {
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
      fetch(import.meta.env.VITE_BACKEND_HOST + "/pokemon/" + pokemon.id, {
        "method": "DELETE",
        "headers": {
          "accept": "*/*"
        }
      })
          .then(response => {
            if (response.ok) {
              this.getPokemonList()
            }
          })
          .catch(err => {
            console.log(err);
          });
    },
    savePokemonForm() {
      if (this.updatingPokemon) {
        this.updatePokemon()
      }
      else {
        this.saveNewPokemon()
      }
    },
    saveNewPokemon() {
      fetch(import.meta.env.VITE_BACKEND_HOST + "/pokemon", {
        "method": "POST",
        "headers": {
          "accept": "application/json",
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          "name": this.pokemonName
        })
      })
          .then(response => {
            if (response.ok) {
              this.getPokemonList()
            }
          })
          .catch(err => {
            console.log(err);
          });
      this.hideModal()
    },
    updatePokemon() {
      fetch(import.meta.env.VITE_BACKEND_HOST + "/pokemon/" + this.pokemonToUpdate.id, {
        "method": "PUT",
        "headers": {
          "accept": "*/*",
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          "name": this.pokemonName
        })
      })
          .then(response => {
            if (response.ok) {
              this.getPokemonList()
            }
          })
          .catch(err => {
            console.log(err);
          });
      this.hideModal()
    },
    // TODO
    getPokemonImgUrl(pokemon) {
      let beginUrl = "https://assets.pokemon.com/assets/cms2/img/pokedex/full/0";
      if (pokemon.id < 10) {
        beginUrl += "0"
      }
      return beginUrl + pokemon.id + ".png"
    },
    getPokemonNo(pokemon) {
      let beginString = "No. 00";
      if (pokemon.id < 10) {
        beginString += "0"
      }
      return beginString + pokemon.id
    },
    getModalTitle() {
      if (this.updatingPokemon) {
        return "Pokémon " + this.getPokemonNo(this.pokemonToUpdate)
      }
      return "Nouveau pokémon"
    }
  },
  beforeMount() {
    this.getPokemonList()
  },
  data() {
    return {
      pokemonList: [],
      pokemonName: "",
      pokemonToUpdate: null,
      updatingPokemon: false,
      modalVisible: false
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

.item-link {
  position: absolute;
  background: none;
  border: none;
  z-index: 1;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
}

.delete-pokemon-button {
  position: absolute;
  z-index: 2;
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
  padding: 120px 0;
}

/* The Modal (background) */
.modal {
  display: block; /* Hidden by default */
  position: fixed; /* Stay in place */
  z-index: 3; /* Sit on top */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0, 0, 0); /* Fallback color */
  background-color: rgba(0, 0, 0, 0.4); /* Black w/ opacity */
}

/* Modal Content/Box */
.modal-content {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #fefefe;
  margin: 15% auto; /* 15% from the top and centered */
  padding: 20px;
  border: 1px solid #888;
  width: 25%; /* Could be more or less, depending on screen size */
}

.modal-content input {
  margin-top: 100px;
  margin-bottom: 75px;
  display: block;
  width: 100%;
  padding: 8px;
  font-size: 24px;
}

.modal-content button {
  background: var(--color-call-to-action) none;
  border: none;
  border-radius: 20px;
  display: flex;
  align-items: center;
  width: 45%;
  height: 50px;
  padding: 8px 8px 8px 16px;
}

.modal-content button:hover {
  background: #d3ad00 none;
}

.modal-content button img {
  height: 100%;
  width: 32px;
  margin-right: 25px;
}

.modal-content legend {
  font-size: 38px;
  display: flex;
  justify-content: center;
  font-family: 'Pokemon Solid', sans-serif;
}
</style>
