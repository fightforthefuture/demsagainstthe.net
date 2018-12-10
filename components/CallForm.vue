<template>
  <div>
    <p v-if="errorMessage" class="text-warn">{{ errorMessage }}</p>
    <form @submit.prevent="submitForm()" class="flex-row">
      <input class="phone sml-flex-2" type="tel" placeholder="Phone Number*"
             v-model.trim="phone" required>
      <button class="btn">
        <span v-if="isSending">Sending...</span>
        <span v-else>Call</span>
      </button>
    </form>
    <p class="sml-push-y1">
      <small>
        Your number will only be used for this call and will never be shared
        with third parties.
        <a href="https://www.battleforthenet.com/privacy/" target="_blank">
          Privacy Policy</a>
      </small>
    </p>
  </div>
</template>

<script>
import axios from 'axios'
import { postFormData } from '~/assets/js/helpers'

export default {

  data() {
    return {
      stateCode: null,
      isSending: false,
      modalVisible: false,
      errorMessage: null
    }
  },

  computed: {
    campaignId() { return this.$store.state.callpowerCampaignId },

    phone: {
      get() {
        return this.$store.state.phone
      },
      set(value) {
        this.$store.commit('setPhone', value)
      }
    }
  },

  methods: {
    async submitForm() {
      this.isSending = true

      try {
        const { data } = await postFormData('https://call-congress.fightforthefuture.org/create', {
          campaignId: this.campaignId,
          userPhone: this.phone
        })

        this.isSending = false
        this.$trackEvent('call_form', 'submit')
        // Show call script in modal
        this.$store.commit('setModalVisibility', true)
        this.$store.commit('setModalType', 'call-script')
        this.phone = ''
      }
      catch (err) {
        this.isSending = false
        this.errorMessage = "Sorry, that didn't work. Please check the phone number and try again."
      }
    }
  }
}
</script>
