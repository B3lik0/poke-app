<template>
  <h1 v-if="!pokemon">Espere por favor...</h1>

  <div v-else>
    <h1>¿Quién es este pokémon?</h1>

    <PokemonPicture :pokemonId="pokemon.id" :showPokemon="showPokemon" />

    <PokemonOptions :pokemons="pokemonArr" @selection-pokemon="checkAnswer" />

    <template v-if="showAnswer">
      <h2 class="fade-in">{{ message }}</h2>
      <button @click="newGame">Nuevo Juego</button>
    </template>
  </div>
</template>

<script lang="ts" setup>
import PokemonOptions from "./PokeOptions.vue";
import PokemonPicture from "./PokePicture.vue";
import getPokemonOptions from "../helpers/getOptions";
import { onMounted, ref } from "vue";
import { IPokemon } from "../interfaces/IPokemon";

const pokemonArr = ref<IPokemon[]>([]);
const pokemon = ref<IPokemon>();
const showPokemon = ref(false);
const showAnswer = ref(false);
const message = ref("");

const mixPokemonArray = async () => {
  pokemonArr.value = await getPokemonOptions();

  const rndInt = Math.floor(Math.random() * 4);
  pokemon.value = pokemonArr.value[rndInt];
};

const checkAnswer = (selectedId: number) => {
  if (!pokemon.value) return false;
  showPokemon.value = true;
  showAnswer.value = true;

  message.value =
    selectedId === pokemon.value?.id
      ? `Correcto, ${pokemon.value?.name}`
      : `Oops, era ${pokemon.value?.name}`;
};
const newGame = () => {
  showPokemon.value = false;
  showAnswer.value = false;
  pokemonArr.value = [];
  pokemon.value = undefined;
  mixPokemonArray();
};
onMounted(() => {
  mixPokemonArray();
});
</script>
