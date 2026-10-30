<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'
import PokemonCard, { type Pokemon } from './components/PokemonCard.vue'

const TOTAL_CARTAS = 40
const MAX_ID = 1025 // total aproximado de Pokémon en la PokeAPI

const pokemons = ref<Pokemon[]>([])
const loading = ref(false)
const error = ref<string | null>(null)

// 40 ids únicos al azar
const randomIds = (count: number): number[] => {
  const ids = new Set<number>()
  while (ids.size < count) ids.add(Math.floor(Math.random() * MAX_ID) + 1)
  return [...ids]
}

const toCard = (data: any): Pokemon => {
  const abilities: string[] = data.abilities.map((a: any) => a.ability.name)
  // Si el Pokémon tiene menos de 2 habilidades, se completa con movimientos
  const moves: string[] = data.moves.map((m: any) => m.move.name)
  while (abilities.length < 2 && moves.length) {
    const move = moves.splice(Math.floor(Math.random() * moves.length), 1)[0]
    if (!abilities.includes(move)) abilities.push(move)
  }
  return {
    id: data.id,
    name: data.name,
    image:
      data.sprites.other?.['official-artwork']?.front_default ?? data.sprites.front_default,
    types: data.types.map((t: any) => t.type.name),
    hp: data.stats.find((s: any) => s.stat.name === 'hp')?.base_stat ?? 0,
    height: data.height,
    weight: data.weight,
    baseExperience: data.base_experience ?? 0,
    abilities: abilities.slice(0, 2),
  }
}

const fetchDeck = async () => {
  loading.value = true
  error.value = null
  try {
    const responses = await Promise.all(
      randomIds(TOTAL_CARTAS).map((id) => axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`)),
    )
    pokemons.value = responses.map((r) => toCard(r.data))
  } catch (err) {
    error.value = 'Error al cargar los datos de los Pokémon.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

onMounted(fetchDeck)
</script>

<template>
  <main class="pokedex">
    <header class="top">
      <h1>Mazo Pokémon</h1>
    </header>

    <p v-if="loading">Cargando cartas...</p>
    <p v-else-if="error" class="error">{{ error }}</p>

    <section v-else class="deck">
      <pokemon-card v-for="p in pokemons" :key="p.id" :pokemon="p" />
    </section>
  </main>
</template>

<style scoped>
.pokedex { max-width: 1800px; margin: 0 auto; padding: 10px 16px; }
.top { margin-bottom: 8px; }
h1 { margin: 0; font-size: 1.4rem; }
.error { color: #c0211f; }
.deck {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  gap: 10px;
}
</style>