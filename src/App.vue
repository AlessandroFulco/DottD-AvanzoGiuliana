<template>
  <div class="container">
    <AppHeader :solved="step==='reveal'" />


    <div class="">
      <QuizStep
          v-if="step==='quiz'"
          class="card"
          :questions="questions"
          @solved="onQuizSolved"
          @retry="onQuizRetry"
      ></QuizStep>
      <PuzzleBoard
          v-else-if="step==='anagram'"
          class="card"
          :error="error"
          :letters="letters"
          :solved="false"
          :hints="hints"
          :hint-index="hintIndex"
          :drag-index="dragIndex"
          :drag-over-index="dragOverIndex"
          @shuffle="shuffle"
          @give-hint="giveHint"
          @check="onAnagramCheck"
          @drag-start="onDragStart"
          @drag-enter="onDragEnter"
          @drag-over="onDragOver"
          @drag-leave="onDragLeave"
          @drop-on="onDrop"
          @drag-end="onDragEnd"

      />

      <RevealPanel v-else class="card" :city="TARGET" />
    </div>


    <div class="confetti" aria-hidden="true" ref="confetti"></div>
  </div>
</template>
<script>
import AppHeader from './components/AppHeader.vue'
import PuzzleBoard from './components/PuzzleBoard.vue'
import RevealPanel from './components/RevealPanel.vue'
import QuizStep from './components/QuizStep.vue'


export default {
  name: 'App',
  components: { AppHeader, PuzzleBoard, RevealPanel, QuizStep },
  data(){
    return {
      error: '',
      TARGET: 'BERLINO', // <-- cambia qui la destinazione
      step: 'quiz', // 'anagram' | 'quiz' | 'reveal'
      letters: [],
      hints: [
        'Porta di Brandeburgo e Reichstag',
        'Currywurst & kebab a qualsiasi ora',
        'Isola dei Musei, East Side Gallery',
        'Codice aeroporto: BER'
      ],
      hintIndex: 0,
      dragIndex: null,
      dragOverIndex: null,
      questions: [
        {
          id: 'q1',
          text: 'Quale autore latino è la principale fonte del mito di Narciso per la letteratura medievale francese?',
          options: ['Ovidio', 'Virgilio', 'Catullo', 'Stazio'],
          answer: 0
        },
        {
          id: 'q2',
          text: 'Nel Medioevo, i miti ovidiani vengono riletti soprattutto in chiave:',
          options: ['Puramente erotica', 'Teologica e allegorica', 'Politica', 'Satirica'],
          answer: 1
        },
        {
          id: 'q3',
          text: 'In quale lingua è composto il Lai de Narcisse?',
          options: ['Occitano', 'Latino tardo', 'Francese antico', 'Anglo-normanno'],
          answer: 2
        },
        {
          id: 'q4',
          text: 'Come viene reinterpretato il mito di Narciso nella letteratura cortese?',
          options: ['Come storia comica di gelosia', 'Come simbolo della purezza dell’amore spirituale', 'Come parabola politica sull’onore feudale', 'Come allegoria del peccato di vanità amorosa'],
          answer: 3
        },
      ]
    }
  },
  mounted(){ this.init() },
  methods:{
    init(){
      this.letters = this.TARGET.split('').map((c, i)=>({ id: i+"-"+c+Math.random().toString(36).slice(2,6), c }))
      this.shuffle()
    },
    shuffle(){
      for(let i=this.letters.length-1;i>0;i--){
        const j = Math.floor(Math.random()*(i+1))
        ;[this.letters[i], this.letters[j]] = [this.letters[j], this.letters[i]]
      }
      if(this.letters.map(l=>l.c).join('') === this.TARGET) this.shuffle()
      this.hintIndex = 0
    },
    giveHint(){ if(this.hintIndex < this.hints.length) this.hintIndex++ },
    onAnagramCheck(){
      const guess = this.letters.map(l=>l.c).join('')
      if(guess === this.TARGET){
        setInterval(() => {
          this.burstConfetti()
        }, 300)
        this.error = ''
      } else {
        this.error = 'Non è la destinazione corretta, usa la mappa per aiutarti!!!'
      }
    },
    onQuizSolved(){
      this.step = 'anagram'
      this.burstConfetti()
    },
    onQuizRetry(){ this.step = 'anagram' },
    onDragStart(idx, e){ this.dragIndex = idx; e.dataTransfer.effectAllowed = 'move' },
    onDragEnter(idx){ this.dragOverIndex = idx },
    onDragOver(idx){ this.dragOverIndex = idx },
    onDragLeave(){ this.dragOverIndex = null },
    onDrop(idx){
      if(this.dragIndex===null) return
      const a = this.dragIndex, b = idx
      ;[this.letters[a], this.letters[b]] = [this.letters[b], this.letters[a]]
      this.dragIndex = null; this.dragOverIndex = null
    },
    onDragEnd(){ this.dragIndex = null; this.dragOverIndex = null },
    burstConfetti(){
      const root = this.$refs.confetti
      if(!root) return
      const N = 60
      for(let i=0;i<N;i++){
        const el = document.createElement('i')
        const hue = Math.floor(180 + Math.random()*160)
        el.style.background = `hsl(${hue} 85% 60%)`
        el.style.left = Math.random()*100 + 'vw'
        el.style.top = - (Math.random()*80 + 20) + 'px'
        el.style.transform = `rotate(${Math.random()*180}deg)`
        el.style.animationDelay = (Math.random()*0.3)+'s'
        root.appendChild(el)
        setTimeout(()=> root.removeChild(el), 2200)
      }
    }
  }
}
</script>
