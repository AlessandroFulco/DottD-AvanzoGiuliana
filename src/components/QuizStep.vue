<template>
  <div class="wrapper-card">
    <section>
      <h1 class="title">Quiz</h1>
      <p class="subtitle">Rispondi correttamente per sbloccare la destinazione ✨</p>


      <div v-for="(q, qi) in questions" :key="q.id" >
        <div v-show="qi == currentQuestion" class="card" style="margin-top:12px">
          <div style="font-weight:700; margin-bottom:8px">{{ qi+1 }}. {{ q.text }}</div>
          <div class="actions">
            <button v-for="(opt, oi) in q.options" :key="oi" :class="['ghost', selected[q.id]===oi ? 'primary' : '']" @click="select(q.id, oi)">
              {{ opt }}
            </button>
          </div>
          <div class="actions-question">
            <button class="ghost" v-if="currentQuestion != 0" @click="previousQuestion(qi)">Indietro</button>
            <button v-if="questions.length == currentQuestion + 1" class="primary" @click="submit">Concludi ✅</button>
            <button v-else class="primary" @click="checkSingleQuestion(qi)">Conferma</button>
          </div>
        </div>
      </div>
      <p v-if="error" class="subtitle" style="color:#fca5a5; margin-top:10px">{{ error }}</p>
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
  components: {
    MapDirections,
  },
  name: 'QuizStep',
  props: { questions: { type: Array, default: () => [] } },
  data(){
    return {
      selected: {},
      error: '',
      currentQuestion: 0,
      waypoints: [
          L.latLng(40.92586952296414, 14.301086851778948),

      ],
      waypointToShow: [
        L.latLng(40.92586952296414, 14.301086851778948),
        L.latLng(45.45665294537729, 9.233980751062917), // milano
        L.latLng(48.091040564100226, 11.437793108640161), // monaco
        L.latLng(50.02670962527501, 9.102809004735327), // francoforte
        L.latLng(52.51604601510939, 13.373207618966356), // BERLINO
      ]
    }
  },
  computed:{
    allAnswered(){ return this.questions.length && this.questions.every(q => this.selected[q.id] !== undefined) }
  },
  methods:{
    select(qid, oi){ this.$set ? this.$set(this.selected, qid, oi) : (this.selected[qid] = oi); this.error='' },
    checkSingleQuestion(qid){
      const ok = this.questions.filter(q => this.selected[q.id] === q.answer)
      if (ok.length > 0) {
        this.currentQuestion = qid + 1;
        this.waypoints.push(this.waypointToShow[qid + 1]);
      } else {
        this.error = 'Mmmh... non sembra essere la risposta corretta, riprova!'
      }
    },
    previousQuestion(qid){
      this.currentQuestion = qid - 1;
    },
    submit(){
      const ok = this.questions.every(q => this.selected[q.id] === q.answer)
      if(ok){ this.$emit('solved') } else { this.error = 'Mmmh... non sembra essere la risposta corretta, riprova!' }
    }
  }
}
</script>
<style>
.actions-question {
  display: flex;
  justify-content: end;
  gap: 8px;
  margin-top: 10px;
}
.wrapper-card {
  display: flex;
  gap: 16px;
}
.wrapper-card > section {
  width: 50%;
}
</style>