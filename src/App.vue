<script setup>
import { ref } from 'vue';
import PhaserCanvas from './components/PhaserCanvas.vue';

const zonaActiva = ref(null);   // { nom, titolMonitor, streamUrl } o null
const refCanvas = ref(null);    // Referència al component pont PhaserCanvas
const modalObert = ref(false);  // Estat de la finestra modal gran

function obrirModalGran() {
  modalObert.value = true;
  refCanvas.value?.bloquejarControls(true);  // CLAU: atura el teclat de Phaser
}

function tancarModalGran() {
  modalObert.value = false;
  refCanvas.value?.bloquejarControls(false); // CLAU: reactiva el teclat de Phaser
}
</script>

<template>
  <main class="layout-oficina">
    <PhaserCanvas ref="refCanvas" @canvi-zona="zonaActiva = $event" />

    <aside class="panell-lateral">
      <h2>Oficina Virtual</h2>
      <p v-if="!zonaActiva" class="estat-passadis">Estàs al passadís (sense àudio/vídeo)</p>

      <!-- Monitor en miniatura: apareix quan la zona té streamUrl -->
      <div v-if="zonaActiva?.streamUrl" class="monitor-mini" @click="obrirModalGran">
        <div class="monitor-capcalera">
          <span>{{ zonaActiva.titolMonitor || zonaActiva.nom }}</span>
          <span>Ampliar</span>
        </div>
        <!-- Punt d'injecció de SimplePeer (:srcObject="remoteStream") -->
        <!-- Si el modal és obert, silenciem el petit per no duplicar el so -->
        <video :src="zonaActiva.streamUrl" :muted="modalObert" autoplay loop playsinline></video>
      </div>
    </aside>

    <!-- Finestra modal gran superposada -->
    <div v-if="modalObert && zonaActiva" class="modal-overlay" @click.self="tancarModalGran">
      <div class="modal-caixa">
        <div class="modal-capcalera">
          <h3>{{ zonaActiva.titolMonitor || zonaActiva.nom }} · Vista Ampliada</h3>
          <button class="boto-tancar" @click="tancarModalGran">Tancar [X]</button>
        </div>
        <video :src="zonaActiva.streamUrl" autoplay controls loop class="video-gran"></video>
      </div>
    </div>
  </main>
</template>

<style scoped>
.layout-oficina { display: flex; gap: 20px; padding: 20px; background: #0f172a; min-height: 100vh; color: #f8fafc; font-family: sans-serif; }
.panell-lateral { width: 320px; background: #1e293b; padding: 16px; border-radius: 12px; border: 1px solid #334155; }
.monitor-mini { margin-top: 16px; background: #0f172a; border: 2px solid #10b981; border-radius: 10px; overflow: hidden; cursor: pointer; transition: transform 0.15s; }
.monitor-mini:hover { transform: scale(1.02); border-color: #34d399; }
.monitor-capcalera { display: flex; justify-content: space-between; padding: 8px 12px; background: #064e3b; font-size: 13px; font-weight: bold; }
.monitor-mini video { width: 100%; display: block; }

/* Finestra modal gran */
.modal-overlay { position: fixed; inset: 0; background: rgba(15, 23, 42, 0.85); display: flex; align-items: center; justify-content: center; z-index: 50; }
.modal-caixa { background: #1e293b; border: 2px solid #38bdf8; border-radius: 14px; width: 90%; max-width: 820px; overflow: hidden; box-shadow: 0 25px 50px rgba(0,0,0,0.6); }
.modal-capcalera { display: flex; justify-content: space-between; align-items: center; padding: 14px 20px; background: #0f172a; border-bottom: 1px solid #334155; }
.boto-tancar { background: #ef4444; color: white; border: none; padding: 6px 14px; border-radius: 6px; font-weight: bold; cursor: pointer; }
.video-gran { width: 100%; max-height: 70vh; display: block; background: black; }
</style>