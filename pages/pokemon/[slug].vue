<script setup>
const query = gql`
query Pokemon($slug: String!) {
  pokemon(where: {slug: $slug}) {
    id
    description
    nom
    slug
    image {
      url(transformation: {document: {output: {format: webp}}})
    }
    poids
    pv
    taille
    pokemonType {
      nom
      couleur {
        hex
      }
    }
    couleur {
      hex
    }
  }
}
`;

const pokemon = ref();
const route = useRoute();
const { data } = await useAsyncQuery(query, {
  slug: route.params.slug,
});
console.log(data.value);
pokemon.value = data.value.pokemon;
</script>

<template>
  <Head v-if="pokemon">
    <Title>{{ pokemon?.nom }} - Détails du Pokémon</Title>
    <Meta name="description" :content="`Découvrez des détails sur ${pokemon?.nom}: ${pokemon?.description}`" />
    <Meta property="og:title" :content="`${pokemon?.nom} - Détails du Pokémon`" />
    <Meta property="og:description" :content="`Découvrez des détails sur ${pokemon?.nom}: ${pokemon?.description}`" />
    <Meta property="og:image" :content="pokemon?.image.url" />
    <Meta property="og:type" content="website" />
    <Meta property="og:locale" content="fr_FR" />
    <Meta name="twitter:card" content="summary_large_image" />
    <Meta name="twitter:title" :content="`${pokemon?.nom} - Détails du Pokémon`" />
    <Meta name="twitter:description" :content="`Découvrez des détails sur ${pokemon?.nom}: ${pokemon?.description}`" />
    <Meta name="twitter:image" :content="pokemon?.image.url" />
  </Head>

  <div v-if="pokemon" class="max-w-lg space-y-8 mx-auto">
    <NuxtImg class="" :src="pokemon?.image.url" :alt="pokemon?.nom" />
    <h2 class="text-3xl text-center">{{ pokemon?.nom }}</h2>
    <p class="text-justify text-red-950">{{ pokemon?.description }}</p>
    <p class="text-justify text-red-950">{{ pokemon?.pv }}</p>
    <p class="text-justify text-red-950">{{ pokemon?.taille }}</p>
    <p class="text-justify text-red-950">{{ pokemon?.poids }}</p>
    <p class="text-justify text-red-950">{{ pokemon?.pokemonType?.nom }}</p>
    <p class="text-justify" :style="{ backgroundColor: pokemon?.pokemonType?.couleur.hex }">{{
      pokemon?.pokemonType?.couleur.hex }}</p>
    <p class="text-justify" :style="{ backgroundColor: pokemon?.couleur.hex }">{{
      pokemon?.couleur.hex }}</p>
  </div>
  <div v-else>
    <li>Loading...</li>
  </div>
</template>
