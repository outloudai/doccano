<template>
  <div>
    <v-card
      v-if="isReady"
      class="title mb-5"
    >
      <v-card-text class="title">
        <audio
          ref="player"
          v-shortkey="{ play: ['esc'], pause: ['f2'], playstart: ['shift', 'f1'], rewind: ['f3'], forward: ['f4'], normal: ['f8'], slow: ['f6'], fast: ['f7'] }"
          controls
          controlsList="nodownload"
          :src="currentDoc.audio"
          @shortkey="playAudio"
        />
      </v-card-text>
    </v-card>
    <seq2seq-box
      v-if="isReady"
      :text="currentDoc.text"
      :annotations="currentDoc.annotations"
      :delete-annotation="_deleteAnnotation"
      :update-annotation="_updateAnnotation"
      :create-annotation="_createAnnotation"
    />
  </div>
</template>

<style scoped>
audio {
  height: 3em;
  width: 100%;
  display: block;
  margin: 0 auto;
}
</style>

<script>
import { mapActions, mapState, mapGetters } from 'vuex'
import Seq2seqBox from '~/components/organisms/annotation/Seq2seqBox'

export default {
  components: {
    Seq2seqBox
  },

  computed: {
    ...mapState('documents', ['loading']),
    ...mapGetters('documents', ['currentDoc']),
    isReady() {
      return this.currentDoc && !this.loading
    }
  },

  methods: {
    ...mapActions('documents', ['getDocumentList', 'deleteAnnotation', 'updateAnnotation', 'addAnnotation']),
    _deleteAnnotation(annotationId) {
      const payload = {
        annotationId,
        projectId: this.$route.params.id
      }
      this.deleteAnnotation(payload)
    },
    _updateAnnotation(annotationId, text) {
      const payload = {
        annotationId,
        text,
        projectId: this.$route.params.id
      }
      this.updateAnnotation(payload)
    },
    _createAnnotation(text) {
      const payload = {
        text,
        projectId: this.$route.params.id
      }
      this.addAnnotation(payload)
    },
    async playAudio(event) {
      const player = this.$refs.player
      switch (event.srcKey) {
        case 'play':
          await player.play()
          break
        case 'pause':
          player.pause()
          break
        case 'playstart':
          player.currentTime = 0.0
          await player.play()
          break
        case 'rewind':
          player.currentTime = player.currentTime - 1
          break
        case 'forward':
          player.currentTime = player.currentTime + 1
          break
        case 'normal':
          player.playbackRate = 1.0
          break
        case 'slow':
          player.playbackRate = 0.6
          break
        case 'fast':
          player.playbackRate = 1.3
          break
      }
    }
  }
}
</script>
