<template>
  <section class="photo-wall-panel position-relative">
    <ul
      class="front-photos d-flex flex-wrap z-20 justify-content-between list-unstyled position-relative"
    >
      <li
        v-for="photo in frontPhotos"
        :key="photo"
        class="photo-front bg-transparent"
      >
        <img class="shadow-lg mx-auto rounded" :src="photo" :alt="photo">
      </li>
    </ul>
    <ul
      class="back-photos position-absolute start-50 z-10 d-flex flex-wrap flex-column list-unstyled"
    >
      <li v-for="photo in backPhotos" :key="photo" class="photo-back">
        <img
          class="shadow-lg rounded"
          src="https://images.unsplash.com/photo-1630702379394-e202e2fbe01e?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=2034&q=80"
          alt=""
        >
      </li>
    </ul>
  </section>
</template>

<script>
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/dist/ScrollTrigger'
import { apiGetPhotosWall } from '@/api'

gsap.registerPlugin(ScrollTrigger)

export default {
  data () {
    return {
      frontPhotos: [],
      backPhotos: []
    }
  },
  created () {
    this.getPhotos()
  },
  mounted () {
    this.setScrollTrigger()
  },
  methods: {
    setScrollTrigger () {
      gsap.to('.front-photos', {
        y: -2000,
        scrollTrigger: {
          trigger: '.photo-wall-panel',
          scrub: 1.5
        }
      })

      gsap.to('.back-photos', {
        y: -1500,
        scrollTrigger: {
          trigger: '.photo-wall-panel',
          scrub: 1.5
        }
      })
    },
    async getPhotos () {
      const frontPhotos = new Array(18).fill()
      const backPhotos = new Array(10).fill()

      try {
        const { data } = await apiGetPhotosWall()
        const frontPhotosKeys = Object.keys(data.photos.front_photos)
        const frontPhotosValues = Object.values(data.photos.front_photos)
        const frontIndexArr = frontPhotosKeys.map(photo =>
          photo.split('-').pop()
        )
        frontIndexArr.forEach((idx, key) => {
          frontPhotos[idx] = frontPhotosValues[key]
        })
        this.frontPhotos = frontPhotos

        const backPhotosKeys = Object.keys(data.photos.back_photos)
        const backPhotosValues = Object.values(data.photos.back_photos)
        const backIndexArr = backPhotosKeys.map(photo => photo.split('-').pop())
        backIndexArr.forEach((idx, key) => {
          backPhotos[idx] = backPhotosValues[key]
        })
        this.backPhotos = backPhotos
      } catch (err) {
        alert(err.response.data.message)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.photo-wall-panel {
  height: 2500px;
}
.front-photos {
  z-index: 10;
}
.back-photos {
  z-index: 0;
  width: 60%;
  top: 300px;
  padding-right: 110px;
  transform: translateX(-50%);
}
.photo-front {
  > img {
    width: 400px;
    height: 300px;
  }
  &:nth-child(3n) {
    width: 100%;
  }
}
.photo-back {
  margin-bottom: 90px;
  > img {
    width: 280px;
    height: 210px;
  }
  &:nth-child(2n) {
    display: flex;
    justify-content: flex-end;
  }
}
</style>
