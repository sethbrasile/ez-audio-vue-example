<script setup lang="ts">
import { createTrack, initAudio, type Track } from 'ez-audio'
import { ref } from 'vue'
import Mp3Player from './components/Mp3Player.vue'

interface Song {
  name: string
  trackInstance?: Track
  description: string
}

const selectedTrack = ref<Song | null>(null)
const loading = ref(false)

const tracks: Song[] = [
  {
    name: 'barely-there',
    trackInstance: null,
    description: `I used to play bass and sing ("clean" vocals) in a metalcore band called "Bringing Down Broadway" and this is one of our songs. The album is titled, "It's all Gone South", I recorded it myself, and it was a complete failure in every measurable way. I think it's awesome.`,
  },
  {
    name: 'do-wah-diddy',
    trackInstance: null,
    description: `My friend David Denison and I recorded this song in a living room with a Korg Electribe, a garbage laptop, and a broken logitech PC mic (we had to literally hold it together while using it), for fun. This is from about 2008. David is "rapping" and I'm singing. If you can't tell, Fallout Boy and that Gym Class Heroes song was popular at the time lol. This is the only place that this song has ever been published.`,
  },
]

async function selectTrack(name: string) {
  selectedTrack.value?.trackInstance.pause()

  loading.value = true
  selectedTrack.value = null

  const track = tracks.find(track => track.name === name)
  if (!track)
    throw (new Error('Balls! Did not find that track!'))
  if (!track.trackInstance) {
    await initAudio()
    track.trackInstance = await createTrack(`node_modules/ez-audio/dist/${name}.mp3`)
  }

  selectedTrack.value = track
  loading.value = false
}
</script>

<template>
  <h1>MP3 Player Example</h1>
  <div class="track-list">
    <div class="select">
      <table>
        <tbody>
          <tr class="pointer" @click="selectTrack('barely-there')">
            <td>Barely There</td>
          </tr>
          <tr class="pointer" @click="selectTrack('do-wah-diddy')">
            <td>Do Wah Diddy</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="description">
      <p v-if="!selectedTrack" id="description">
        Select a track...
      </p>
      <p v-else>
        {{ selectedTrack.description }}
      </p>
    </div>
  </div>

  <Mp3Player v-if="selectedTrack?.trackInstance" :track="selectedTrack.trackInstance" />

  <div v-if="loading" class="spinner">
    <div class="rect1" />
    <div class="rect2" />
    <div class="rect3" />
    <div class="rect4" />
    <div class="rect5" />
  </div>
</template>

<style scoped>
.pointer {
  cursor: pointer;
}

.track-list table {
  margin-bottom: 0;
}
.track-list .item {
  text-align: center;
}
</style>
