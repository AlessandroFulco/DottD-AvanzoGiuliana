<template>
  <div>
    <section>
      <h1 class="title">Metti in ordine le lettere</h1>
      <p class="subtitle">Trascina i riquadri finché la parola non ha senso. Quando indovini, sveliamo la destinazione! 😉</p>


      <div class="tiles" role="list">
        <div v-for="(ch, idx) in letters" :key="ch.id" class="tile" role="listitem"
             draggable="true"
             :aria-label="'Lettera ' + ch.c"
             @dragstart="emitDragStart(idx, $event)"
             @dragenter.prevent="emitDragEnter(idx)"
             @dragover.prevent="emitDragOver(idx)"
             @dragleave="emitDragLeave()"
             @drop.prevent="emitDrop(idx)"
             @dragend="emitDragEnd"
             :class="{dragover: dragOverIndex===idx, dragging: dragIndex===idx}">
          {{ ch.c }}
        </div>
      </div>


      <div class="wrapper-actions">
        <div class="actions">
          <button class="ghost" @click="$emit('shuffle')" :disabled="solved">Mescola 🔀</button>
          <button class="primary" @click="$emit('check')" :disabled="solved">Controlla ✅</button>
        </div>
        <p v-if="error" class="subtitle" style="color:#fca5a5; margin-top:10px">{{ error }}</p>
      </div>


      <div class="hint" v-if="hintIndex>0">
        <strong>Indizio {{ hintIndex }}:</strong> {{ hints[hintIndex-1] }}
        <div class="progress" aria-label="Progresso indizi"><i :style="{width: (hintIndex/hints.length*100)+'%'}"></i></div>
      </div>
    </section>
    <section>
      <MapDirections
          :waypoints="waypoints"
          :zoom="12"
      />
    </section>
  </div>

</template>


<script>
import MapDirections from "@/components/MapDirections.vue";
import L from "leaflet";

export default {
  name: 'PuzzleBoard',
  components: {MapDirections},
  data() {
    return {
      waypoints: [
        L.latLng(40.92586952296414, 14.301086851778948),
        L.latLng(45.45665294537729, 9.233980751062917), // milano
        L.latLng(48.091040564100226, 11.437793108640161), // monaco
        L.latLng(50.02670962527501, 9.102809004735327), // francoforte
        L.latLng(52.51604601510939, 13.373207618966356), // BERLINO
      ]
    }
  },
  props: {
    error:  { type: String },
    letters: { type: Array, required: true },
    solved: { type: Boolean, default: false },
    hints: { type: Array, default: () => [] },
    hintIndex: { type: Number, default: 0 },
    dragIndex: { type: Number, default: null},
    dragOverIndex: {type: Number, default: null}
  },
  methods: {
    emitDragStart(idx, e) {
      this.$emit('drag-start', idx, e)
    },
    emitDragEnter(idx) {
      this.$emit('drag-enter', idx)
    },
    emitDragOver(idx) {
      this.$emit('drag-over', idx)
    },
    emitDragLeave() {
      this.$emit('drag-leave')
    },
    emitDrop(idx) {
      this.$emit('drop-on', idx)
    },
    emitDragEnd() {
      this.$emit('drag-end')
    }
  }
}
</script>
<style scoped>
.wrapper-actions {
  display: flex;
  flex-direction: column;
  align-items: end;
  margin-bottom: 20px;
}
.actions {
  display: flex;
  gap: 1rem;
  align-items: center;
  justify-content: end;
}
</style>