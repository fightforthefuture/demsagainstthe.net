<template>
  <div>
    <PageHeader/>

    <nuxt/>

    <PageFooter class="sml-push-y3 med-push-y6"/>
  </div>
</template>

<script>
import PageHeader from '~/components/PageHeader'
import PageFooter from '~/components/PageFooter'

export default {
  components: {
    PageHeader,
    PageFooter
  }
}
</script>
