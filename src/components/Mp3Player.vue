<script setup lang="ts">
import type { Track } from 'ez-audio'
import { computed } from 'vue'

const props = defineProps<{
  track: Track
}>()

const emits = defineEmits(['togglePlay', 'seek', 'changeGain'])
const percentPlayed = computed(() => `width: ${props.track.percentPlayed}%;`)
const percentGain = computed(() => `height: ${props.track.percentGain}%;`)

function seek(e: any) {
  const width = e.target.offsetParent.offsetWidth
  const newPosition = e.offsetX / width
  emits('seek', newPosition)
}

function changeVolume(e: any) {
  const height = e.target.offsetParent.offsetHeight
  const parentOffset
      = e.target.parentNode.getBoundingClientRect().top + window.scrollY
  const offset = e.pageY - parentOffset - document.documentElement.clientTop
  const adjustedHeight = height * 0.8
  const adjustedOffset = offset - (height - adjustedHeight) / 2
  const newGain = adjustedOffset / adjustedHeight
  emits('changeGain', newGain)
}
</script>

<template>
  <div class="audioplayer">
    <div role="button" class="play-pause" :class="{ playing: track.isPlaying }" @click="emits('togglePlay')">
      <a />
    </div>
    <div class="time current">
      {{ track.position }}
    </div>

    <div role="button" class="bar" @click="seek">
      <div style="width: 100%;" />
      <div class="played" :style="percentPlayed" />
    </div>

    <div class="time duration">
      {{ track.duration }}
    </div>

    <div role="button" class="volume" @click="changeVolume">
      <div class="button">
        <a />
      </div>

      <div class="adjust">
        <div>
          <div :style="percentGain" />
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="css">
.selected {
  color: gray;
  font-size: 1.2em;
}

.spinner {
  width: 50px;
  height: 40px;
}

.spinner > div {
  background-color: #337ab7;
  height: 100%;
  width: 6px;
  display: inline-block;

  -webkit-animation: sk-stretchdelay 1.2s infinite ease-in-out;
  animation: sk-stretchdelay 1.2s infinite ease-in-out;
}

.spinner .rect2 {
  -webkit-animation-delay: -1.1s;
  animation-delay: -1.1s;
}

.spinner .rect3 {
  -webkit-animation-delay: -1.0s;
  animation-delay: -1.0s;
}

.spinner .rect4 {
  -webkit-animation-delay: -0.9s;
  animation-delay: -0.9s;
}

.spinner .rect5 {
  -webkit-animation-delay: -0.8s;
  animation-delay: -0.8s;
}

@-webkit-keyframes sk-stretchdelay {
  0%, 40%, 100% { -webkit-transform: scaleY(0.4) }
  20% { -webkit-transform: scaleY(1.0) }
}

@keyframes sk-stretchdelay {
  0%, 40%, 100% {
    transform: scaleY(0.4);
    -webkit-transform: scaleY(0.4);
  }
  20% {
    transform: scaleY(1.0);
    -webkit-transform: scaleY(1.0);
  }
}

.audioplayer {
  height: 2.5em;
  color: #fff;
  border: 1px solid #337ab7;
  position: relative;
  z-index: 1;
  background: #337ab7;
  border-radius: 2px;
}
.audioplayer > div {
  position: absolute;
}
.play-pause {
  border-right: 1px solid #555;
  border-right-color: rgba(255,255,255,0.1);
  width: 2.5em;
  height: 100%;
  text-align: left;
  text-indent: -9999px;
  cursor: pointer;
  z-index: 2;
  top: 0;
  left: 0;
}
.play-pause:not(.playing) a {
  width: 0;
  height: 0;
  border: 0.5em solid transparent;
  border-right: none;
  border-left-color: #fff;
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  margin: -0.5em 0 0 -0.25em;
}
.play-pause.playing a {
  width: 0.75em;
  height: 0.75em;
  position: absolute;
  top: 50%;
  left: 50%;
  margin: -0.375em 0 0 -0.375em;
}
.play-pause.playing a:before,
.play-pause.playing a:after {
  width: 40%;
  height: 100%;
  background-color: #fff;
  content: '';
  position: absolute;
  top: 0;
}
.play-pause.playing a:before {
  left: 0;
}
.play-pause.playing a:after {
  right: 0;
}
.time {
  width: 4.375em;
  height: 100%;
  line-height: 2.375em;
  text-align: center;
  z-index: 2;
  top: 0;
}
.current {
  border-left: 1px solid #111;
  border-left-color: rgba(0,0,0,0.25);
  left: 2.5em;
}
.duration {
  border-right: 1px solid #555;
  border-right-color: rgba(255,255,255,0.1);
  right: 2.5em;
}
.audioplayer-novolume .duration {
  border-right: 0;
  right: 0;
}
.bar {
  height: 0.875em;
  background-color: #337ab7;
  cursor: pointer;
  z-index: 1;
  top: 50%;
  right: 6.875em;
  left: 6.875em;
  margin-top: -0.438em;
}
.audioplayer-novolume .bar {
  right: 4.375em;
}
.bar div {
  width: 0;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
}
.bar-loaded {
  background-color: #337ab7;
  z-index: 1;
}
.played {
  background: #d9edf7;
  z-index: 2;
}
.volume {
  width: 2.5em;
  height: 100%;
  border-left: 1px solid #111;
  border-left-color: rgba(0,0,0,0.25);
  text-align: left;
  text-indent: -9999px;
  cursor: pointer;
  z-index: 2;
  top: 0;
  right: 0;
}
.volume:hover,
.volume:focus {
  background-color: #337ab7;
}
.button {
  width: 100%;
  height: 100%;
}
.button a {
  width: 0.313em;
  height: 0.45em;
  background-color: #fff;
  display: block;
  position: relative;
  z-index: 1;
  top: 40%;
  left: 35%;
}
.button a:before,
.button a:after {
  content: '';
  position: absolute;
}
.button a:before {
  width: 0;
  height: 0;
  border: 0.5em solid transparent;
  border-left: none;
  border-right-color: #fff;
  z-index: 2;
  top: 50%;
  right: -0.25em;
  margin-top: -0.5em;
}
.adjust {
  height: 6.25em;
  cursor: default;
  position: absolute;
  left: 0;
  right: -1px;
  top: -9999px;
  background: #337ab7;
  border-top-left-radius: 2px;
  border-top-right-radius: 2px;
}
.volume:not(:hover) .adjust {
  opacity: 0;
}
.volume:hover .adjust {
  top: auto;
  bottom: 100%;
}
.adjust > div {
  width: 40%;
  height: 80%;
  background-color: #337ab7;
  cursor: pointer;
  position: relative;
  z-index: 1;
  margin: 30% auto 0;
}
.adjust div div {
  width: 100%;
  height: 100%;
  position: absolute;
  bottom: 0;
  left: 0;
  background-color: #d9edf7;
}
.audioplayer-novolume .volume {
  display: none;
}
.bar,
.bar div,
.adjust div {
  border-radius: 4px;
}
.bar,
.adjust > div {
  box-shadow: -1px -1px 0 rgba(0,0,0,0.5), 1px 1px 0 rgba(255,255,255,0.1);
}
.audioplayer *,
.audioplayer *:before,
.audioplayer *:after {
  transition: color 0.25s ease, background-color 0.25s ease, opacity 0.5s ease;
}
</style>
