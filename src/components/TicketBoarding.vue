<template>
  <div class="ticket">
    <div>
      <h3>Boarding Pass</h3>
      <div class="divider"></div>
      <div class="muted">Passeggero</div>
      <div style="font-weight:700; font-size:18px">Tu ✨</div>
      <div class="muted" style="margin-top:10px">Da</div>
      <div style="font-weight:700">Sorpresa</div>
      <div class="muted" style="margin-top:10px">A</div>
      <div style="font-weight:800; font-size:20px">{{ code }} • {{ city }}</div>
      <div class="muted" style="margin-top:10px">Messaggio</div>
      <div>Prepara la valigia: si parte! 🧳</div>
    </div>
    <div>
      <div class="qr"></div>
      <div class="muted" style="margin-top:8px">Gate ✦ B R N</div>
    </div>
  </div>
</template>


<script>
export default {
  name: 'TicketBoarding',
  props: { city: { type: String, default: 'BERLINO' }, code: { type: String, default: 'BER' } }
}
</script>