<template>
  <aside aria-live="polite">
    <template v-if="!solved">
      <h2 class="title">Obiettivo 🎯</h2>
      <p class="subtitle">Ricomponi la parola corretta. È una capitale europea famosa per street‑art, techno e musei...</p>
      <ul class="subtitle" style="margin-top:10px; padding-left:18px">
        <li>Puoi <strong>trascinare</strong> i riquadri per riordinarli</li>
        <li>Usa <strong>Indizio</strong> se ti blocchi</li>
        <li>Premi <strong>Controlla</strong> per verificare</li>
      </ul>
    </template>
    <template v-else>
      <div class="reveal">
        <div class="success"><span>✅</span><strong>Destinazione svelata!</strong></div>
        <h2 class="title">{{ city }} 🇩🇪</h2>
        <TicketBoarding :city="city" code="BER" />
        <div class="footer">Vuoi personalizzare data/compagnia/immagine? È tutto a componenti.</div>
      </div>
    </template>
  </aside>
</template>
<script>
import TicketBoarding from './TicketBoarding.vue'
export default {
  name: 'RevealPanel',
  components: { TicketBoarding },
  props: { solved: Boolean, city: String }
}
</script>