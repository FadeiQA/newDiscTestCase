<template>
  <v-stage
    ref="stage"
    :config="configKonva"
  >
    <v-layer ref="layer">
      <v-image
        ref="image"
        :config="configKonvaImage"/>
      <v-transformer ref="transformer" />
    </v-layer>
  </v-stage>
</template>

<script>
import {mapState} from "vuex";

export default {
  name: "PicViewer",
  data() {
    return {
      id: this.$route.params.id || 1,
      configKonva: {
        width: 0,
        height: 0
      },
      configKonvaImage: {
        image: null,
        draggable: true,
      }
    }
  },

  computed: {
    ...mapState(['pictures', "options"]),
    imageScale() {
      return this.options.imageScale
    }
  },

  watch: {
    imageScale() {
      this.initImage()
    }
  },

  mounted() {
    this.initImage()
  },

  methods: {
    URLCreate(img) {
      return URL.createObjectURL(img)
    },
    initImage() {
      if (!this.pictures || !this.pictures[this.id]) {
        return this.$router.push('/')
      }
      const image = new Image();
      image.src = this.URLCreate(this.pictures[this.id])
      const vueThis = this
      image.onload = function () {
          let futureHeight = this.height
          let futureWidth = this.width
          switch (vueThis.options.imageScale) {
            case 'native':
              break
            case 'contain':
              futureHeight = window.innerHeight
              futureWidth = window.innerHeight * this.width / this.height

              if (futureWidth > window.innerWidth) {
                futureWidth = window.innerWidth
                futureHeight = window.innerWidth * this.width / this.height
              }
              break
            case 'width':
              futureWidth = window.innerWidth
              futureHeight = window.innerWidth * this.width / this.height
              break
            case 'height':
              futureHeight = window.innerHeight
              futureWidth = window.innerHeight * this.width / this.height
              break
          }
        vueThis.configKonva.height = window.innerHeight
        vueThis.configKonva.width = window.innerWidth
        this.height = futureHeight
        this.width = futureWidth
        vueThis.configKonvaImage.image = this;
      };
    }
  },
}
</script>

<style scoped lang="scss">
.asdf {
  height: 100vh;
  width: 100vh;
}

</style>
