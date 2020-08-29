<template>
  <seq2seq-container />
</template>

<script>
import Seq2seqContainer from '~/components/containers/annotation/Speech2textContainer'

export default {
  layout: 'annotation',

  components: {
    Seq2seqContainer
  },

  validate({ params, query }) {
    return /^\d+$/.test(params.id) && /^\d+$/.test(query.page)
  }
}
</script>
