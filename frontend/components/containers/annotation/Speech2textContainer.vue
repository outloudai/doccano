<template>
  <div>
    <v-card
      v-if="isReady"
      class="title mb-5"
    >
      <v-card-text class="title">
        <audio
          ref="player"
          v-shortkey="{ pn: ['f1'], pns: ['shift', 'f1'], rewindb: ['f2'], rewindf: ['shift', 'f2'], slower: ['f3'], faster: ['f4'] }"
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
      const skey = event.srcKey
      if (skey === 'pn') {
        if (this.isAudioPlaying) {
          player.pause()
          this.isAudioPlaying = false
        } else {
          await player.play()
          this.isAudioPlaying = true
        }
      } else if (skey === 'pns') {
        player.currentTime = 0.0
        await player.play()
        this.isAudioPlaying = true
      } else if (skey === 'slower') {
        if (player.playbackRate > 1) {
          player.playbackRate = 1.0
        } else if (player.playbackRate > 0.9) {
          player.playbackRate = 0.6
        }
      } else if (skey === 'faster') {
        if (player.playbackRate < 0.9) {
          player.playbackRate = 1.0
        } else if (player.playbackRate < 1.1) {
          player.playbackRate = 1.3
        }
      } else if (skey === 'rewindb') {
        player.currentTime = player.currentTime - 1
      } else if (skey === 'rewindf') {
        player.currentTime = player.currentTime + 1
      }
    }
  }
}
</script>
