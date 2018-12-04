<template>
  <div>
    <section class="sml-pad-y3 med-pad-y6">
      <div class="wrapper">
        <div class="row">
          <div class="sml-c12 lrg-c9 grid-center text-center">
            <h1 class="sml-push-y3"><span class="highlight">
              These Are The Democrats Helping Trump and Ajit Pai Kill Net Neutrality
            </span></h1>
            <p class="sml-push-y3">
              Despite a vast majority of Americans who support net neutrality,
              a handful of Dems are putting the telecom lobby&rsquo;s money over
              the will of their constituents. What do all of these hold out Dems
              have in common? They&rsquo;ve all taken tens of thousands of
              dollars in &ldquo;campaign donations&rdquo; from telecoms like
              Verizon, Comcast, and AT&amp;T.
            </p>
            <p><strong>
              Now, these Dems have
              <a href="https://www.deadlinefornetneutrality.com/">
                just a few more days</a>
              to save the Internet as we know it by signing the Congressional
              Review Act discharge petition. Will they stand with the American
              people or will they stand with Trump and his FCC crony Ajit Pai?
            </strong></p>

            <a @click.prevent="scrollTo('#dems')">
              <img src="~assets/images/arrow-down.svg" alt="down arrow"
                   class="grid-center sml-push-y4 med-push-y6"/>
            </a>
          </div> <!-- .c -->
        </div> <!-- .row -->
      </div> <!-- .wrapper -->
    </section>

    <section id="dems">
      <div class="wrapper">
        <div class="row">
          <div class="sml-c12 med-c7 lrg-c10 grid-center">
            <TargetReps :reps="reps"/>
          </div> <!-- .c -->
        </div> <!-- .row -->
      </div> <!-- .wrapper -->
    </section>
  </div>
</template>

<script>
import axios from 'axios'
import config from '~/config'
import { createMetaTags, smoothScrollToElement } from '~/assets/js/helpers'
import TargetReps from '~/components/TargetReps'

export default {
  components: {
    TargetReps
  },

  head() {
    return {
      title: config.sharing.title,
      meta: createMetaTags({
        title: config.sharing.title,
        description: config.sharing.description,
        image: config.sharing.image,
        url: config.sharing.url
      })
    }
  },

  async asyncData() {
    let reps = []
    try {
      const { data } = await axios.get('https://data.battleforthenet.com/demsagainstthenet/dems.json')
      reps = data
    }
    catch (error) {
      //
    }
    return {
      reps: reps
    }
  },

  methods: {
    scrollTo(hash) {
      const duration = 500
      smoothScrollToElement(hash, duration)
      // WARNING: Since there is no server a setTimeout is ok. However, with a
      // server this is a dangerous eval. Remove if this project ever is hosted
      // with a JS server.
      setTimeout(() => {
        location.hash = hash
      }, duration)
    }
  }
}
</script>
