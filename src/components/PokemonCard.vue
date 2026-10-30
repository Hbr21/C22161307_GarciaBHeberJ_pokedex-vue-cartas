<script setup lang="ts">
import { computed } from 'vue'

export interface Pokemon {
  id: number
  name: string
  image: string
  types: string[]
  hp: number
  height: number // decímetros
  weight: number // hectogramos
  baseExperience: number
  abilities: string[]
}

const props = defineProps<{ pokemon: Pokemon }>()

const typeColors: Record<string, string> = {
  normal: '#a8a77a', fire: '#ee8130', water: '#6390f0', electric: '#f7d02c',
  grass: '#7ac74c', ice: '#96d9d6', fighting: '#c22e28', poison: '#a33ea1',
  ground: '#e2bf65', flying: '#a98ff3', psychic: '#f95587', bug: '#a6b91a',
  rock: '#b6a136', ghost: '#735797', dragon: '#6f35fc', dark: '#705746',
  steel: '#b7b7ce', fairy: '#d685ad',
}

const typesEs: Record<string, string> = {
  normal: 'Normal', fire: 'Fuego', water: 'Agua', electric: 'Eléctrico',
  grass: 'Planta', ice: 'Hielo', fighting: 'Lucha', poison: 'Veneno',
  ground: 'Tierra', flying: 'Volador', psychic: 'Psíquico', bug: 'Bicho',
  rock: 'Roca', ghost: 'Fantasma', dragon: 'Dragón', dark: 'Siniestro',
  steel: 'Acero', fairy: 'Hada',
}

const mainColor = computed(() => typeColors[props.pokemon.types[0]] ?? '#a8a77a')
const secondColor = computed(() => typeColors[props.pokemon.types[1] ?? props.pokemon.types[0]] ?? '#a8a77a')

const cardStyle = computed(() => ({
  '--c1': mainColor.value,
  '--c2': secondColor.value,
}))

const pad = (n: number) => String(n).padStart(3, '0')
const label = (s: string) => s.replace(/-/g, ' ').replace(/^./, (c) => c.toUpperCase())
</script>

<template>
  <article class="pokemon-card" :style="cardStyle">
    <div class="card-inner">
      <header class="card-header">
        <h2 class="name">{{ label(pokemon.name) }}</h2>
        <span class="hp"><small>PS</small> {{ pokemon.hp }}</span>
      </header>

      <div class="art">
        <img :src="pokemon.image" :alt="pokemon.name" loading="lazy" />
      </div>

      <div class="strip">
        <span class="id">ID {{ pad(pokemon.id) }}</span>
        <span v-for="t in pokemon.types" :key="t" class="type" :style="{ background: typeColors[t] }">
          {{ typesEs[t] ?? t }}
        </span>
      </div>

      <ul class="abilities">
        <li v-for="(a, i) in pokemon.abilities" :key="a">
          <span class="energy">{{ i + 1 }}</span>
          <span class="ability-name">{{ label(a) }}</span>
        </li>
      </ul>

      <dl class="traits">
        <div><dt>Altura</dt><dd>{{ (pokemon.height / 10).toFixed(1) }} m</dd></div>
        <div><dt>Peso</dt><dd>{{ (pokemon.weight / 10).toFixed(1) }} kg</dd></div>
        <div><dt>Exp. base</dt><dd>{{ pokemon.baseExperience }}</dd></div>
      </dl>
    </div>
  </article>
</template>

<style scoped>
.pokemon-card {
  container-type: inline-size;
  aspect-ratio: 63 / 88;
  padding: 4px;
  border-radius: 8px;
  background: linear-gradient(145deg, #f6d95b, #c9a227);
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.3);
  transition: transform 0.2s;
}
.pokemon-card:hover { transform: translateY(-3px); }

.card-inner {
  height: 100%;
  display: flex;
  flex-direction: column;
  gap: 3cqw;
  padding: 4cqw;
  border-radius: 5px;
  background: linear-gradient(160deg, color-mix(in srgb, var(--c1) 45%, #fff), color-mix(in srgb, var(--c2) 45%, #fff));
  color: #1b1b1b;
  overflow: hidden;
}

.card-header { display: flex; justify-content: space-between; align-items: baseline; gap: 2cqw; }
.name { margin: 0; font-size: 8cqw; text-transform: capitalize; line-height: 1.1; }
.hp { font-weight: 800; color: #c0211f; white-space: nowrap; font-size: 8cqw; }
.hp small { font-size: 5cqw; color: #1b1b1b; }

.art {
  flex: 1;
  min-height: 0;
  display: grid;
  place-items: center;
  border: 2px solid #d4af37;
  border-radius: 4px;
  background: radial-gradient(circle at 50% 40%, #fff, color-mix(in srgb, var(--c1) 35%, #fff));
}
.art img { max-width: 85%; max-height: 95%; object-fit: contain; }

.strip { display: flex; align-items: center; gap: 2cqw; font-size: 5.5cqw; }
.id { font-weight: 700; margin-right: auto; }
.type { color: #fff; padding: 0.5cqw 3.5cqw; border-radius: 999px; font-weight: 700; text-shadow: 0 1px 1px rgba(0, 0, 0, 0.4); }

.abilities { list-style: none; margin: 0; padding: 0; display: grid; gap: 1.5cqw; }
.abilities li {
  display: flex; align-items: center; gap: 3cqw;
  padding: 1.5cqw 2.5cqw; border-radius: 4px; background: rgba(255, 255, 255, 0.65);
  font-size: 6cqw; font-weight: 600;
}
.energy {
  display: grid; place-items: center; width: 8cqw; height: 8cqw; border-radius: 50%;
  background: var(--c1); color: #fff; font-size: 5cqw; flex-shrink: 0;
}

.traits { display: flex; justify-content: space-between; margin: 0; padding-top: 1.5cqw; border-top: 1px solid rgba(0, 0, 0, 0.25); font-size: 5cqw; text-align: center; }
.traits dt { font-weight: 700; }
.traits dd { margin: 0; }
</style>