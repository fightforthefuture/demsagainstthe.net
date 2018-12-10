<template>
  <div>
    <div class="flex-row sml-flex-col med-flex-row sml-push-y3 med-push-y4">
      <div>
        <h3>Senators</h3>
        <p v-for="(rep, index) in senate"
           :key="`s-${index}-${rep.first_name}`"
           :class="{'sml-push-y2': index === 0}">
          Sen. {{rep.first_name}} {{rep.last_name}}
        </p> <!-- v-for -->
      </div> <!-- .flex-row -->
      <div>
        <h3 class="sml-push-y3 med-push-y0">House Members</h3>
        <p v-for="(rep, index) in house"
           :key="`rep-${index}-${rep.first_name}`"
           :class="{'sml-push-y2': index === 0}">
          Rep. {{rep.first_name}} {{rep.last_name}}
        </p> <!-- v-for -->
      </div> <!-- .flex-row -->
      <div>
        <h3 class="sml-push-y3 med-push-y0">Organizations</h3>
        <p v-for="(org, index) in orgs"
           :key="`org-${index}-${org}`"
           :class="{'sml-push-y2': index === 0}">
          {{ org }}
        </p> <!-- v-for -->
      </div>
      <div>
        <h3 class="sml-push-y3 med-push-y0">People</h3>
        <p v-for="(p, index) in people"
           :key="`p-${index}-${p}`"
           :class="{'sml-push-y2': index === 0}">
          {{ p }}
        </p> <!-- v-for -->
      </div>
    </div> <!-- .flex-row -->
    <div class="sml-push-y2 med-push-y4">
      <a @click.prevent="toggleAll" v-text="showAll ? 'Show less' : 'Show all'"></a>
    </div>
  </div>
</template>

<script>
import ORGS from '~/assets/data/supporting-orgs.json'
import PEOPLE from '~/assets/data/supporting-people.json'

export default {
  props: {
    reps: {
      type: Array,
      required: true,
      default: null
    }
  },

  data() {
    return {
      showAll: false,
      numPreviewItems: 10
    }
  },

  computed: {
    orgs() {
      return this.showAll ? ORGS : ORGS.slice(0, this.numPreviewItems)
    },
    people() {
      return this.showAll ? PEOPLE : PEOPLE.slice(0, this.numPreviewItems)
    },
    senate() {
      let s = this.reps.filter(r => r.organization === 'Senate')
      return this.showAll ? s : s.slice(0, this.numPreviewItems)
    },
    house() {
      let h = this.reps.filter(r => r.organization === 'House')
      return this.showAll ? h : h.slice(0, this.numPreviewItems)
    }
  },

  methods: {
    toggleAll() {
      this.showAll = !this.showAll
    }
  }
}
</script>
