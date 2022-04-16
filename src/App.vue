<template>
  <div
    id="app"
    :class="{ 'has-mouse': hasMouse }"
    @touchstart="hasMouse = false"
  >
    <Flipbook
      class="flipbook"
      :pages="pages"
      :pagesHiRes="pagesHiRes"
      :startPage="pageNum"
      v-slot="flipbook"
      ref="flipbook"
      @flip-left-start="onFlipLeftStart"
      @flip-left-end="onFlipLeftEnd"
      @flip-right-start="onFlipRightStart"
      @flip-right-end="onFlipRightEnd"
      @zoom-start="onZoomStart"
      @zoom-end="onZoomEnd"
    >
      <div class="view-pdf">Having trouble viewing the invitation? <br/> <br/><a href="https://bit.ly/mico-and-grace" target="_blank">Click here to view the PDF.</a></div>
      <div class="action-bar">
        <left-icon
          class="btn left"
          :class="{ disabled: !flipbook.canFlipLeft }"
          @click="flipbook.flipLeft"
        />
        <minus-icon
          class="btn minus"
          :class="{ disabled: !flipbook.canZoomOut }"
          @click="flipbook.zoomOut"
        />
        <span class="page-num">
          Page {{ flipbook.page }} of {{ flipbook.numPages }}
        </span>
        <plus-icon
          class="btn plus"
          :class="{ disabled: !flipbook.canZoomIn }"
          @click="flipbook.zoomIn"
        />
        <right-icon
          class="btn right"
          :class="{ disabled: !flipbook.canFlipRight }"
          @click="flipbook.flipRight"
        />
      </div>
    </Flipbook>
  </div>
</template>

<script lang="coffee">
import 'vue-material-design-icons/styles.css'
import Ribbon from 'vue-ribbon'
import LeftIcon from 'vue-material-design-icons/ChevronLeftCircle'
import RightIcon from 'vue-material-design-icons/ChevronRightCircle'
import PlusIcon from 'vue-material-design-icons/PlusCircle'
import MinusIcon from 'vue-material-design-icons/MinusCircle'
import Flipbook from './Flipbook'

export default
  name: 'app'
  components: { Flipbook, LeftIcon, RightIcon, PlusIcon, MinusIcon, Ribbon }
  data: ->
    pages: [],
    pagesHiRes: [],
    hasMouse: true
    pageNum: null
  methods:
    setPhotos: () ->
       # Simulate asynchronous pages initialization
      setTimeout (=>
        vw = Math.max(document.documentElement.clientWidth || 0, window.innerWidth || 0);

        if vw < 444
          @pages = [
            null
            'images/1.jpg'
            'images/2.jpg'
            'images/3.jpg'
            'images/7.jpg'
            'images/4.jpg'
          ]
          @pagesHiRes = [
            null
            'images-large/1.jpg'
            'images-large/2.jpg'
            'images-large/3.jpg'
            'images-large/7.jpg'
            'images-large/4.jpg'
          ]
        else
          @pages = [
            null
            'images/1.jpg'
            'images/2.jpg'
            'images/3.jpg'
            'images/5.jpg'
            'images/6.jpg'
            'images/4.jpg'
          ]
          @pagesHiRes = [
            null
            'images-large/1.jpg'
            'images-large/2.jpg'
            'images-large/3.jpg'
            'images-large/5.jpg'
            'images-large/6.jpg'
            'images-large/4.jpg'
          ]
      ),
    onFlipLeftStart: (page) -> console.log 'flip-left-start', page
    onFlipLeftEnd: (page) ->
      console.log 'flip-left-end', page
      window.location.hash = '#' + page
    onFlipRightStart: (page) -> console.log 'flip-right-start', page
    onFlipRightEnd: (page) ->
      console.log 'flip-right-end', page
      window.location.hash = '#' + page
    onZoomStart: (zoom) -> console.log 'zoom-start', zoom
    onZoomEnd: (zoom) -> console.log 'zoom-end', zoom
    setPageFromHash: ->
      n = parseInt window.location.hash.slice(1), 10
      @pageNum = n if isFinite n
  mounted: ->
    window.addEventListener 'keydown', (ev) =>
      flipbook = @$refs.flipbook
      return unless flipbook
      flipbook.flipLeft() if ev.keyCode == 37 and flipbook.canFlipLeft
      flipbook.flipRight() if ev.keyCode == 39 and flipbook.canFlipRight

    @setPhotos();
    window.addEventListener 'hashchange', @setPageFromHash
    window.addEventListener 'resize', @setPhotos
    @setPageFromHash()
</script>

<style>
@import "~normalize.css/normalize.css";

body {
  background-color: #fefbf6;
  display: flex;
  place-items: center;
  justify-content: center;
  max-height: 100vh;
  min-height: 100vh;
}
.view-pdf {
  text-align: center;
  font-size: 8px;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  max-height: 100vh;
  padding-block: 2rem;
  background-color: #fefbf6;
  color: #808080;
}

a {
  color: inherit;
}

.action-bar {
  width: 100%;
  height: 30px;
  padding: 10px 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.action-bar .btn {
  font-size: 30px;
  color: #BAA88C;
}

.action-bar .btn svg {
  bottom: 0;
}

.action-bar .btn:not(:first-child) {
  margin-left: 10px;
}

.has-mouse .action-bar .btn:hover {
  cursor: pointer;
}

.action-bar .btn:active {
  filter: none !important;
}

.action-bar .btn.disabled {
  color: #ccc;
  pointer-events: none;
}

.action-bar .page-num {
  font-size: 12px;
  margin-left: 10px;
}

.flipbook {
  display: flex;
  flex-direction: column-reverse;
  gap: 10px;
}

.flipbook .viewport {
  width: 90vw;
  height: 70vh;
}

.flipbook .bounding-box {
  box-shadow: 0 10px 10px rgba(194, 185, 167, 0.3);
}

@media screen and (min-width: 1024px) {
  .flipbook .viewport {
    height: calc(90vh - 50px);
  }
}
</style>
