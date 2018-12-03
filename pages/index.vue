<template>
  <div>
    <section class="sml-pad-y3 med-pad-y6">
      <div class="wrapper">
        <div class="row">
          <div class="sml-c12 lrg-c8 grid-center text-center">
            <h1>Your title goes here</h1>
            <p class="sml-push-y2 med-push-y3">
              Sub heading goes here, lorem ipsum dolor sit amet, consectetur
              adipiscing elit. In nibh libero, venenatis sed justo eu,
              sollicitudin sollicitudin nisi. Integer semper tortor orci,
              id ultricies velit laoreet in. Vestibulum sit amet ante vel risus
              ornare ultrices sed id leo.
            </p>
            <a class="btn btn-block sml-push-y2 med-push-y3" href="#TODO">
              Call to action
            </a>
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

export default {
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
    let events = []
    try {
      const { data } = await axios.get('https://data.battleforthenet.com/events.json')

      events = data.filter(e => e.category === 'facebook_group').sort((a, b) => {
        if (a.address < b.address) {
          return -1
        }
        else if (a.address > b.address) {
          return 1
        }
        else {
          return 0
        }
      })
    }
    catch (error) {
      //
    }
    return {
      events: events
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
