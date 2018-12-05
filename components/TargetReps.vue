<style lang="scss">
.rep-name {
  line-height: 1.3;
}
.rep-container {
  @include respond-to(lrg) {
    max-width: 31.722%;
  }
}
.rep-image-container {
  position: relative;
  max-width: 200px; // Width of rep image
  max-height: 220px;
  overflow: hidden;
  border-radius: $default-border-radius;
}
.rep-image-container:after {
  position: absolute;
  content: '';
  display: block;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  border-radius: $default-border-radius;
  background: transparentize($warn-color, 0.5);
}
</style>

<template>
  <div>
    <div v-for="(row, rowIndex) in repRows" :key="`row-${rowIndex}`"
         class="flex-row sml-flex-col lrg-flex-row"
         :class="{'lrg-push-y4': rowIndex > 0}">
      <div v-for="(rep, repIndex) in row"
           :key="`rep-${repIndex}-${rep.name}`"
           class="rep-container">
        <div class="sml-push-y2 lrg-push-y0 sml-pad-1 fill-warn is-rounded-top">
          <p class="text-center"><strong>
           {{ rep.cable_contributions }} from telecoms
          </strong></p>
        </div> <!-- .fill -->
        <div class="sml-pad-1 sml fill-white">
          <div class="rep-image-container grid-center sml-push-y1">
            <img :src="`https://data.battleforthenet.com/scoreboard-images/${rep.bioguide_id}.jpg`"
                 :alt="`Photo of ${rep.name}`"
                 class="is-rounded grid-center">
          </div> <!-- .rep-image-container -->
          <p class="sml-push-y1 rep-name text-center">
            <strong>Rep. {{ rep.name }}</strong><br>
            ({{ rep.district }})
          </p>
        </div> <!-- .fill -->
        <div class="sml-pad-1 sml-pad-y1 fill-grey with-border-bottom">
          <p class="text-brand">
            <img class="icon-left" src="~assets/images/phone.svg" alt="Phone"/>
            <a :href="`tel:${rep.phone}`"><strong>{{ rep.phone }}</strong></a>
          </p>
        </div> <!-- .fill -->
        <div class="sml-pad-1 sml-pad-y1 fill-grey is-rounded-bottom">
          <p class="text-brand">
            <img class="icon-left" src="~assets/images/twitter-logo.svg" alt="Twitter"/>
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
