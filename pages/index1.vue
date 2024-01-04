<script setup>
const query = gql`
  query Pokemons {
  pokemons {
    id
    nom
    slug
    image {
      url(
        transformation: {image: {resize: {height: 256, width: 256}}, document: {output: {format: webp}}}
      )
    }
  }
}
`;

const pokemons = ref();
const { data } = await useAsyncQuery(query);
console.log(data.value);
pokemons.value = data.value.pokemons;
</script>

<template>
  <ul v-if="pokemons" class="grid gap-8 grid-cols-2 sm:grid-cols-3 lg:grid-cols-4 2xl:grid-cols-6">
    <li v-for="pokemon in pokemons" :key="pokemon.id">
      <NuxtLink :to="`/pokemon/${pokemon.slug}`">
        <NuxtImg :src="pokemon.image.url" :alt="pokemon.nom" />
        <h2 class="text-3xl text-center">{{ pokemon.nom }}</h2>
      </NuxtLink>
    </li>
  </ul>
  <ul v-else>
    <li>Loading...</li>
  </ul>
</template>