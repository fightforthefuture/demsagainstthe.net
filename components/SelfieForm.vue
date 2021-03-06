<style lang="scss">
@keyframes blur-in {
  0% {
    opacity: 0;
    filter:blur(25px);
  }
  100% {
    opacity: 1;
    filter:blur(0px);
  }
}

.preview-container {
  overflow: hidden;
  position: relative;

  video {
    width: 100%;
    height: 100%;
    object-fit: cover;
    animation: blur-in 2s;
  }

  .overlay {
    object-fit: cover;
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    pointer-events: none;
  }
}
</style>

<template>
  <div>
    <div v-if="!hasSubmitted">
      <form @submit.prevent="submitForm()" class="sml-push-y2 med-push-y3">
        <div class="sml-push-y2 med-push-y3 sml-pad-2 fill-grey is-rounded-top">
          <p class="text-warn text-center" v-if="errorMessage">{{ errorMessage }}</p>
          <div class="flex-row sml-flex-col med-flex-row" :class="{'sml-push-y-half': errorMessage}">
            <div class="">
              <div class="preview-container grid-center is-rounded"
                   :style="{ width: previewWidth, height: previewHeight }"
                   @click="clickPreview">
                <img v-if="!photoSource && !videoStream"
                     src="~assets/images/photo-default.png"
                     alt="Your image goes here"
                     ref="placeholderImage"/>
                <img v-else-if="photoSource" :src="photoSource" ref="photo">

                <!-- This always needs to be present in the DOM, but should only be
                     visible if there's a video stream -->
                <video v-show="videoStream" autoplay ref="liveView"></video>

                <!-- This will be combined with the uploaded photo -->
                <img v-show="!photoSource" src="~/assets/images/photo-frame.png" alt="" ref="overlay" class="overlay">
              </div>

              <div class="flex-row sml-push-y1">
                <div v-if="hasWebcam">
                  <a class="btn btn-block btn-sml"
                     @click.prevent="startLiveView()"
                     :disabled="isCapturing"
                     v-html="captureButtonHtml">
                  </a>
                </div> <!-- v-if -->

                <div>
                  <a class="btn btn-block btn-sml btn-alt"
                     @click.prevent="openFilePicker()"
                     :disabled="isCapturing">
                    <img src="/photo-upload.svg">Upload
                  </a>
                  <input type="file"
                         v-show="false"
                         @change="uploadPhoto"
                         :disabled="isCapturing"
                         ref="fileInput">
                </div>
              </div> <!-- .flex-row -->
            </div> <!-- .c -->
            <div class="flex-row sml-flex-col sml-push-y2 med-push-y0">
              <label class="flex-fixed-height sml-pad-1 sml-pad-x2 fill-grey-light is-rounded-top">
                <h5>Your Net Neutrality thoughts:</h5>
              </label>
              <textarea v-model="comment" class="flat-top"
                        placeholder="I care about Net Neutrality because...">
              </textarea>
            </div> <!-- .c -->
          </div> <!-- .row -->
        </div> <!-- .fill -->
        <div class="sml-pad-2 fill-grey-dark is-rounded-bottom">
          <button class="btn btn-block" :disabled="isSending || !photoSource">
            <span v-if="isSending">
              Sending...
            </span>
            <span v-else>
              Submit my photo
            </span>
          </button>
        </div> <!-- .fill -->
      </form>
      <audio preload="auto" ref="countdownSound">
        <source src="/sounds/countdown.mp3">
      </audio>
      <audio preload="auto" ref="shutterSound">
        <source src="/sounds/shutter.mp3">
      </audio>
    </div> <!-- v-if -->

    <SelfieFormComplete v-if="hasSubmitted"/>
  </div>
</template>

<script>
import axios from 'axios'
import { createPNG } from '~/assets/js/selfie'
import { isMobileOS } from '~/assets/js/helpers'
import SelfieFormComplete from '~/components/SelfieFormComplete'

export default {
  components: {
    SelfieFormComplete
  },

  data() {
    return {
      videoStream: null,
      previewWidth: '',
      previewHeight: '',
      isCapturing: false,
      captureButtonHtml: "<img src='/photo.svg'>Take Photo",
      hasWebcam: false,
      isSending: false,
      hasSubmitted: false,
      errorMessage: null,
      comment: null
    }
  },

  computed: {
    photoSource: {
      get() {
        return this.$store.state.photoSource
      },

      set(value) {
        this.$store.commit('setPhotoSource', value)
      }
    }
  },

  created() {
    this.timers = {}
  },

  async mounted() {
    if (this.$refs.placeholderImage) {
      const self = this
      this.$refs.placeholderImage.onload = function() {
        self.previewWidth = `${this.width}px`
        self.previewHeight = `${this.height}px`
        this.onload = null
      }
    }

    const devices = await navigator.mediaDevices.enumerateDevices()
    const videoDevices = devices.filter(d => d.kind === 'videoinput')

    if (videoDevices.length > 0 && !isMobileOS()) {
      this.hasWebcam = true
    }
  },

  destroyed() {
    this.stopLiveView()

    for (let key of Object.keys(this.timers)) {
      clearTimeout(this.timers[key])
    }
  },

  methods: {
    playSound(name) {
      this.$refs[`${name}Sound`].play()
    },

    handleLiveViewError() {
      this.hasWebcam = false
      this.isCapturing = false
      this.videoStream = null
      this.errorMessage = "Couldn't connect to camera 😞"
    },

    async startLiveView() {
      this.photoSource = null

      try {
        const constraits = {
          video: {
            width: 1280,
            height: 720,
            facingMode: 'user'
          },
          audio: false
        }

        const stream = await navigator.mediaDevices.getUserMedia(constraits)

        if (!stream.active) {
          throw new Error('Received inactive video stream')
        }

        // this can't be bound via :srcObject for some reason
        this.$refs.liveView.srcObject = stream

        this.videoStream = stream
        this.isCapturing = true
        this.captureButtonHtml = 'Starting...'

        // some browsers will just hang forever if your laptop is in clamshell mode
        this.timers.captureFail = setTimeout(() => {
          console.error('Failed to start live view!')
          this.handleLiveViewError()
        }, 5000)

        // start countdown when video is ready
        this.$refs.liveView.oncanplay = () => {
          this.countdown(3)
          clearTimeout(this.timers.captureFail)
        }
      }
      catch (error) {
        console.error(error)
        this.handleLiveViewError()
      }
    },

    stopLiveView() {
      if (this.videoStream) {
        this.videoStream.getTracks()[0].stop()
      }
    },

    countdown(seconds) {
      if (seconds > 0) {
        this.captureButtonHtml = `Ready in ${seconds}…`
        this.playSound('countdown')

        this.timers.countdown = setTimeout(() => {
          this.countdown(seconds-1)
        }, 1000);
      }
      else {
        this.playSound('shutter')
        this.takePhoto()
      }
    },

    async createSelfie({ source, file }) {
      this.photoSource = await createPNG({
        sourceElement: source,
        sourceFile: file,
        overlayElement: this.$refs.overlay
      })

      this.isCapturing = false
      this.stopLiveView()
      this.captureButtonHtml = "<img src='/photo-retake.svg'>Retake Photo"
    },

    takePhoto() {
      this.createSelfie({
        source: this.$refs.liveView
      })
      this.$trackClick('take_photo')
    },

    uploadPhoto(event) {
      this.createSelfie({
        file: event.target.files[0]
      })
      this.$trackClick('upload_photo')
    },

    openFilePicker() {
      this.$refs.fileInput.click()
    },

    clickPreview() {
      if (this.hasWebcam) {
        this.startLiveView()
      }
      else {
        this.openFilePicker()
      }
    },

    async submitForm() {
      if (this.isSending) return

      this.isSending = true

      try {
        // TODO: genericize URL or change per project
        const { data } = await axios.post('https://deadline.fftf.xyz/selfies', {
          photo: this.photoSource,
          comment: this.comment,
          name: this.$store.state.name,
          email: this.$store.state.email,
          zip_code: this.$store.state.zipCode
        })

        this.$trackEvent('selfie_form', 'submit')
        this.isSending = false
        this.$store.commit('setSelfie', data)
        this.hasSubmitted = true
      }
      catch (err) {
        this.isSending = false
        this.errorMessage = "Sorry, that didn’t work for some reason. Please try again."
      }
    },
  }
}
</script>
