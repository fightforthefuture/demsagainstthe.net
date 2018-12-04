<style lang="scss">
.rep-name {
  line-height: 1.2;
  min-height: $base-font-size*2*1.2;
}
</style>

<template>
  <div>
    <div v-for="(row, rowIndex) in repRows" :key="`row-${rowIndex}`"
         class="flex-row sml-flex-col med-flex-row sml-push-y2">
      <div v-for="(rep, repIndex) in row" :key="`rep-${repIndex}-${rep.name}`">
        <div class="sml-push-y2 med-push-y0 sml-pad-2 fill-white is-rounded-top">
          <img :src="`https://data.battleforthenet.com/scoreboard-images/${rep.bioguide_id}.jpg`"
               :alt="`Photo of ${rep.name}`"
               class="is-rounded grid-center">
          <p class="sml-push-y1 rep-name">
            <strong>Rep. {{ rep.name }}</strong>
            ({{ rep.district }})
          </p>
        </div> <!-- .fill -->
        <div class="sml-pad-2 sml-pad-y1 fill-grey with-border-bottom">
          <p class="text-brand">
            <img class="icon-left" src="~assets/images/phone.svg" alt="Phone"/>
            Call:
            <a :href="`tel:${rep.phone}`"><strong>{{ rep.phone }}</strong></a>
          </p>
        </div> <!-- .fill -->
        <div class="sml-pad-2 sml-pad-y1 fill-grey is-rounded-bottom">
          <p class="text-brand">
            <img class="icon-left" src="~assets/images/twitter-logo.svg" alt="Twitter"/>
            Tweet:
            <a :href="`https://twitter.com/${rep.twitter}`" target="_blank"><strong>
              @{{ rep.twitter }}</strong></a>
          </p>
        </div> <!-- .fill -->
      </div> <!-- .fill -->
    </div>
  </div>
</template>

<script>
export default {
  props: {
    reps: {
      type: Array,
      required: true,
      default: null
    }
  },

  computed: {
    repRows() {
      let repRows = []
      let index=0
      for (index = 0; index < this.reps.length; index += 3) {
        let repRow = this.reps.slice(index, index+3);
        repRows.push(repRow);
      }
      return repRows
    }
  }
}
</script>
