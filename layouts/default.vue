<template>
    <Head>
        <Title>Pokedex IA</Title>
        <Meta name="description"
            content="Explorez un Pokedex complet et interactif propulsé par IA. Découvrez des Pokémon uniques avec des informations détaillées et des images générées par intelligence artificielle." />
        <Meta property="og:description" content="Découverte Interactive de Pokémon | Pokedex Complet Alimenté par IA" />
        <Meta property="og:image" content="https://nuxt-hygraph-example.vercel.app/img/pokedex.webp" />
        <Meta property="og:url" content="https://nuxt-hygraph-example.vercel.app/" />
        <Meta property="og:type" content="website" />
        <Meta property="og:locale" content="fr_FR" />
        <Meta property="og:site_name" content="Pokedex IA" />
        <Meta name="twitter:card" content="summary_large_image" />
        <Meta name="twitter:site" content="@CastorObese" />
        <Meta name="twitter:creator" content="@CastorObese" />
        <Meta name="twitter:title" content="Pokedex IA" />
        <Meta name="twitter:description" content="Découverte Interactive de Pokémon | Pokedex Complet Alimenté par IA" />
        <Meta name="theme-color" content="#DC143C" />
    </Head>

    <div class="min-h-screen bg-slate-600">
        <nav class="container mx-auto px-3 h-24 flex items-center mb-5">
            <NuxtLink to="/">
                <h1 class="text-5xl font-serif text-white">Pokedex</h1>
            </NuxtLink>
        </nav>
        <div class="container mx-auto px-3">
            <slot />
        </div>
    </div>
</template>