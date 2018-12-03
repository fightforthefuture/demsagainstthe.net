<template>
  <div>
    <section class="sml-pad-y3 med-pad-y6">
      <div class="wrapper">
        <div class="row">
          <div class="sml-c12 lrg-c8 grid-center text-center">
            <h1>
              These Are The Democrats Helping Trump and Ajit Pai Kill Net Neutrality
            </h1>
            <p class="sml-push-y2 med-push-y3">
              More than 8 in 10 Americans from across the political divide
              support net neutrality—but you wouldn&rsquo;t know it watching
              things play out in D.C. Despite 75% of Republican voters
              supporting net neutrality, GOP lawmakers have been virtually
              absent from supporting all Internet freedom fights as they cave
              to the will Trump and Ajit Pai. Meanwhile, the vast majority of
              Democratic lawmakers have taken a stand to defend net neutrality—
              <strong>except for a handful of Dems who are putting the Internet
              lobby&rsquo;s money over the will of their constituents.</strong>
            </p>
            <p>
              What do all of these hold out Dems have in common? They&rsquo;ve
              all taken tens of thousands of dollars in &ldquo;campaign
              donations&rdquo; from telecoms like Verizon, Comcast, and AT&amp;T.
            </p>
            <p><strong>
              Now, these Dems have
              <a href="https://www.deadlinefornetneutrality.com/">
                just a few more days</a>
              to save the Internet as we know it by signing the Congressional
              Review Act discharge petition. Will they stand with the American
              people or will they stand with Trump and his FCC crony Ajit Pai?
            </strong></p>
          </div> <!-- .c -->
        </div> <!-- .row -->
      </div> <!-- .wrapper -->
    </section>

    <section id="dems">
      <div class="wrapper">
        <div class="row">
          <div class="sml-c12 lrg-c10 grid-center text-center">
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
