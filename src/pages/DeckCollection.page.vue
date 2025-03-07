<template>
  <div class="deck-container">
    <h1>DeckBuilder</h1>
    <h2>Liste des Cartes Pokémon</h2>
    <p>Construisez votre deck en sélectionnant vos cartes Pokémon préférées.</p>

    <n-grid cols="4" responsive="screen" :x-gap="20" :y-gap="20">
      <n-gi v-for="card in pokemonCards" :key="card.id">
        <n-card class="pokemon-card" hoverable>
          <img :src="card.imageUrl" :alt="card.name" class="pokemon-img" />
          <div class="pokemon-info">
            <h3>{{ card.name }}</h3>
            <p>
              <strong>PV :</strong> {{ card.hp }}
              <n-tag :type="getTagType(card.type)">{{ card.type }}</n-tag>
            </p>
            <p>
              <strong>Attaque :</strong> {{ card.attack }}
              <span class="attack-power">{{ card.attackDamage }} PV</span>
            </p>
          </div>
        </n-card>
      </n-gi>
    </n-grid>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue';
import { NCard, NTag, NGrid, NGi } from 'naive-ui';

interface PokemonCard {
  id: number;
  name: string;
  imageUrl: string;
  type: string;
  hp: number;
  attack: string;
  attackDamage: number;
}

const pokemonCards = ref<PokemonCard[]>([]);

const API_URL = 'https://pokemon-api-seyrinian-production.up.railway.app/pokemon-cards';

const getTagType = (type: string) => {
  const typeColors: Record<string, string> = {
    Fire: 'warning',
    Water: 'info',
    Grass: 'success',
    Electric: 'warning',
    Normal: 'default',
    Psychic: 'info',
    Fighting: 'error',
    Flying: 'info',
    Poison: 'warning',
    Ground: 'warning',
    Rock: 'default',
    Bug: 'success',
    Ghost: 'info',
    Steel: 'default',
    Ice: 'info',
    Dragon: 'warning',
    Dark: 'error',
    Fairy: 'success',
  };
  return typeColors[type] || 'default';
};

onMounted(async () => {
  try {
    const response = await fetch(API_URL);
    const data = await response.json();
    pokemonCards.value = data.map((card: any) => ({
      id: card.id,
      name: card.name,
      imageUrl: card.imageUrl,
      type: card.type,
      hp: card.hp,
      attack: card.attack,
      attackDamage: card.attackDamage,
    }));
  } catch (error) {
    console.error('Erreur lors du chargement des cartes Pokémon :', error);
  }
});
</script>

<style scoped>
.deck-container {
  padding: 20px;
  text-align: center;
}

.pokemon-card {
  text-align: center;
  border-radius: 12px;
  padding: 10px;
}

.pokemon-img {
  width: 100px;
  height: auto;
}

.pokemon-info h3 {
  margin: 10px 0 5px;
}

.attack-power {
  color: red;
  font-weight: bold;
}
</style>