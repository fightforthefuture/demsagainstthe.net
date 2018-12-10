<style lang="scss" scoped>
  .icon-call {
    max-height: $font-size-2*1.2;
  }
  .icon-warning {
    max-height: $base-font-size*$base-line-height;
  }
</style>

<template>
  <div>
    <div class="with-border-bottom">
      <h2>
        <img src="~/assets/images/call-icon.svg" alt="Phone icon"
             class="img-inline icon-call">
        Calling you now&hellip;
      </h2>
      <p class="sml-push-y1"><strong>Introduce yourself, be polite, and say:</strong></p>
      <p class="text-meta">
        I&rsquo;m calling on all House Democrats to sign the discharge petition
        before the end of this Congress to force a vote on the net neutrality CRA.
      </p>

      <div class="sml-pad-y2">
        <p>
          <img src="~/assets/images/warning-circle.svg" alt="!"
               class="img-inline icon-warning">
          If staffers dismiss you because you are not a constituent, don&rsquo;t
          let them hang up. Remind them time is running out for their boss to do
          the right thing.
        </p>
      </div>
    </div>
    <h3 class="sml-push-y2">Done calling? Do these things, too!</h3>

    <SocialShareButtons class="sml-push-y1"/>
  </div>
</template>


<script>
import SocialShareButtons from '~/components/SocialShareButtons'

export default {
  components: {
    SocialShareButtons
  }
}
</script>
