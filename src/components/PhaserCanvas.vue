<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import Phaser from 'phaser';
import { OfficeScene } from '../game/OfficeScene.js';

const contenidorJoc = ref(null);
let game = null;

// Esdeveniment que escoltarà el pare (App.vue)
const emit = defineEmits(['canviZona']);

// Funció pont: rep l'ordre de App.vue i la delega a l'escena de Phaser
function bloquejarControls(estat) {
  const escena = game?.scene.getScene('OfficeScene');
  if (escena) {
    escena.bloquejarControls(estat);
  }
}

// CLAU: connexió Vue -> Phaser
defineExpose({ bloquejarControls });

onMounted(() => {
  game = new Phaser.Game({
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    parent: contenidorJoc.value,
    physics: { default: 'arcade', arcade: { debug: false } },
    scene: [OfficeScene],
  });

  // Connexió Phaser -> Vue: reenvia { nom, titolMonitor, streamUrl } cap amunt
  game.events.on('canvi-zona', (dadaZona) => {
    emit('canviZona', dadaZona);
  });
});

onBeforeUnmount(() => {
  game?.destroy(true);
});
</script>

<template>
  <div ref="contenidorJoc" class="marc-canvas"></div>
</template>