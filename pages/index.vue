<template>
  <div>
    <section class="sml-pad-y3 med-pad-y6">
      <div class="wrapper">
        <div class="row">
          <div class="sml-c12 lrg-c10 grid-center text-center">
            <h1 class="sml-push-y3"><span class="highlight">
              These Are The Democrats Helping Trump and Ajit Pai Kill Net Neutrality
            </span></h1>
            <p class="sml-push-y3">
              The vast majority of Americans support net neutrality, but a handful of 
              House Democrats are 
              <a href="https://gizmodo.com/last-minute-push-to-restore-net-neutrality-stymied-by-d-1831023390">holding out</a> 
              support. Why? They’ve all taken tens of thousands of dollars in “campaign 
              donations” from powerful Internet service providers like AT&T, Comcast and 
              Verizon. Now, Congress has just a few more days to save the Internet as we 
              know it and we need these Democrats are on our side. <strong>Enter your 
              number now to tell them to side with the American people by committing to 
              real net neutrality.</strong>
            </p>

            <CallForm class="sml-push-y4 med-push-y6"/>
          </div> <!-- .c -->
        </div> <!-- .row -->
      </div> <!-- .wrapper -->
    </section>

    <section id="dems">
      <div class="wrapper">
        <div class="row">
          <div class="sml-c12 med-c7 lrg-c10 grid-center">
            <TargetReps :reps="reps.oppose" v-if="reps.oppose"/>
          </div> <!-- .c -->
        </div> <!-- .row -->
      </div> <!-- .wrapper -->
    </section>

    <section id="supporters" class="sml-push-y6 med-push-y8">
      <div class="wrapper">
        <div class="row">
          <div class="sml-c12 grid-center text-center">
            <h2>
              Lawmakers, Businesses, and the grassroots voices on the right side
              of Internet history
            </h2>

            <LogoCloud :reps="reps.support" v-if="reps.support"/>
          </div> <!-- .c -->
        </div> <!-- .row -->
      </div> <!-- .wrapper -->
    </section>

    <Modal>
      <CallScriptModal v-if="modalType === 'call-script'"/>
    </Modal>
  </div>
</template>

<script>
import axios from 'axios'
import config from '~/config'
import { mapState } from 'vuex'
import { createMetaTags, smoothScrollToElement } from '~/assets/js/helpers'
import CallForm from '~/components/CallForm'
import TargetReps from '~/components/TargetReps'
import LogoCloud from '~/components/LogoCloud'
import Modal from '~/components/Modal'
import CallScriptModal from '~/components/CallScriptModal'

export default {
  components: {
    CallForm,
    TargetReps,
    LogoCloud,
    Modal,
    CallScriptModal
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
    let reps = {}
    try {
      const { data } = await axios.get('https://data.battleforthenet.com/demsagainstthenet/dems.json')
      reps.oppose = data
    }
    catch (error) {
      //
    }

    try {
      const { data } = await axios.get('https://data.battleforthenet.com/scoreboard/all.json')
      reps.support = data.filter(r => r.supports_cra)
    }
    catch (error) {
      //
    }

    return {
      reps: reps
    }
  },

  computed: {
    ...mapState(['modalType', 'modalData'])
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
