<script setup>
const queryPokemonList = gql`
  query Pokemons {
  pokemons(first: 20) {
    id
    nom
    slug
    image {
      url(
        transformation: {image: {resize: {height: 256, width: 256}}, document: {output: {format: webp}}}
      )
    }
    pokemonType {
      nom
    }
  }
  pokemonTypes {
    nom
  }
}
`;

const querySelectedPokemon = gql`
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
      image {
        url(transformation: {document: {output: {format: webp}}})
      }
    }
    couleur {
      hex
    }
    attaques {
      degat
      description
      nom
      image {
        url(transformation: {document: {output: {format: webp}}})
      }
    }
  }
}

`;

const pokemons = ref();
const typeSelect = ref('all');
const selectedPokemon = ref(null);

async function selectPokemon(slug) {
  const { data } = await useAsyncQuery(querySelectedPokemon, {
    slug: slug,
  });
  selectedPokemon.value = data.value.pokemon;
}

// Chargement initial de la liste des Pokémon
const { data: pokemonData } = await useAsyncQuery(queryPokemonList);
pokemons.value = pokemonData.value.pokemons;
const types = ref(pokemonData.value.pokemonTypes);

const searchTxt = ref(''); // Termes de recherche
const pokemonsFiltered = computed(() => {
  let filtered = pokemons.value;

  if (typeSelect.value && typeSelect.value !== 'all') {
    filtered = filtered.filter(pokemon => pokemon.pokemonType && pokemon.pokemonType.nom === typeSelect.value);
  }

  if (searchTxt.value) {
    const term = searchTxt.value.toLowerCase();
    filtered = filtered.filter(pokemon => pokemon.nom.toLowerCase().includes(term));
  }

  return filtered;
});
</script>

<template>
  <Head v-if="selectedPokemon">
    <Title>{{ selectedPokemon?.nom }} - Détails du Pokémon</Title>
    <Meta name="description"
      :content="`Découvrez des détails sur ${selectedPokemon?.nom}: ${selectedPokemon?.description}`" />
    <Meta property="og:title" :content="`${selectedPokemon?.nom} - Détails du Pokémon`" />
    <Meta property="og:description"
      :content="`Découvrez des détails sur ${selectedPokemon?.nom}: ${selectedPokemon?.description}`" />
    <Meta property="og:image" :content="selectedPokemon?.image.url" />
    <Meta property="og:type" content="website" />
    <Meta property="og:locale" content="fr_FR" />
    <Meta name="twitter:card" content="summary_large_image" />
    <Meta name="twitter:title" :content="`${selectedPokemon?.nom} - Détails du Pokémon`" />
    <Meta name="twitter:description"
      :content="`Découvrez des détails sur ${selectedPokemon?.nom}: ${selectedPokemon?.description}`" />
    <Meta name="twitter:image" :content="selectedPokemon?.image.url" />
  </Head>
  <div class="flex">
    <div class="bg-red-500 w-1/2 p-5 rounded-l-lg h-[82vh]">
      <div class="h-[100%]">
        <div class="py-5 px-3 space-x-4 bg-zinc-600 rounded-t-lg shadow-xl">
          <input type="text" v-model="searchTxt" placeholder="Rechercher un pokémon">
          <label for="type" class="text-white">Filtrer par type:</label>
          <select name="type" id="type" v-model="typeSelect">
            <option value="all" selected>ALL</option>
            <option v-for="pokeType in types" :value="pokeType.nom" :key="pokeType.nom">{{
              pokeType.nom }}</option>
          </select>
        </div>
        <ul v-if="pokemonsFiltered.length || !searchTxt" class="block overflow-scroll bg-zinc-400 p-2 rounded-b-lg"
          style="height: calc(100% - 64px);">
          <li v-for="pokemon in pokemonsFiltered.length ? pokemonsFiltered : pokemons" :key="pokemon.id">
            <button @click="selectPokemon(pokemon.slug)" :class="[
              'group', 'w-full', 'aspect-w-1/1', 'border-2', 'flex', 'items-center', 'my-5', 'hover:bg-zinc-300', 'hover:text-blue-500', 'rounded-full', 'transition',
              pokemon.nom === selectedPokemon?.nom ? ['bg-blue-500', 'underline', 'text-white'] : 'bg-gray-500'
            ]">
              <NuxtImg :src="pokemon.image.url" :alt="pokemon.nom"
                class="inline-block h-12 w-12 self-center bg-blue-200 rounded-l-full" />
              <h2 class="text-3xl text-left ml-2 text-white">{{ pokemon.nom }}</h2>
            </button>
          </li>
        </ul>
        <ul v-else>
          <li>Loading...</li>
        </ul>
      </div>
    </div>
    <div v-if="selectedPokemon" :style="{ backgroundColor: selectedPokemon?.couleur.hex }"
      class="w-1/2 p-5 rounded-r-lg h-[82vh]">
      <div class="bg-white rounded-lg h-full p-5 space-y-4 overflow-scroll">
        <NuxtImg class="h-80 m-auto rounded-full" :src="selectedPokemon?.image.url" :alt="selectedPokemon?.nom" />
        <h2 class="text-3xl text-center font-bold">{{ selectedPokemon?.nom }}</h2>
        <p class="text-justify">{{ selectedPokemon?.description }}</p>
        <hr class="border-black border-1 my-3">
        <ul class="grid grid-cols-2 space-y-4 place-items-center">
          <li class="col-span-2"><span class="font-bold">Couleur:</span>
            <div :style="{ backgroundColor: selectedPokemon?.couleur.hex }"
              class="w-6 h-6 inline-block rounded-full ml-2">
            </div>
            {{
              selectedPokemon?.couleur.hex }}
          </li>
          <li><span class="font-bold">Type:</span><img class="h-10 inline-block ml-2 rounded-full"
              :src="selectedPokemon?.pokemonType?.image.url" :alt="selectedPokemon?.pokemonType?.nom"> {{
                selectedPokemon?.pokemonType?.nom }}
          </li>
          <li><span class="font-bold">PV:</span> {{ selectedPokemon?.pv }}</li>
          <li><span class="font-bold">Taille:</span> {{ selectedPokemon?.taille }} m</li>
          <li><span class="font-bold">Poids:</span> {{ selectedPokemon?.poids }} kg</li>
        </ul>
        <hr class="border-black border-1 my-3">
        <h3 class="font-bold text-md">Attaques</h3>
        <ul class="grid grid-cols-2 place-items-center gap-10">
          <li v-for="attaque in selectedPokemon?.attaques" class="m-0">
            <ul class="space-y-4 place-items-center">
              <li class="font-bold"><img class="h-10 inline-block mr-2 rounded-full" :src="attaque?.image.url"
                  :alt="attaque?.nom">{{
                    attaque?.nom }}
              </li>
              <li>{{ attaque?.description }}</li>
              <li><span class="font-bold">Dégats:</span> {{ attaque?.degat }}</li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
</div></template>