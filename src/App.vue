<script setup lang="ts">
import type { Track } from 'ez-audio'
import { createTrack, initAudio } from 'ez-audio'
import { computed, ref } from 'vue'
import Mp3Player from './components/Mp3Player.vue'
import { titleCase } from './utils'

interface Song {
  name: string
  trackInstance?: Track
  description: string
}

const loading = ref(false)
const selectedSong = ref<Song | null>(null)
// @ts-expect-error TS is doing something weird here
const track = computed<Track | undefined>(() => selectedSong.value?.trackInstance)

// "barely-there.mp3" and "do-wah-diddy.mp3" are mp3 files located in this project's assets folder
const songs: Song[] = [
  {
    name: 'barely-there',
    description: `I used to play bass and sing ("clean" vocals) in a metalcore band called "Bringing Down Broadway" and this is one of our songs. The album is titled, "It's all Gone South", I recorded it myself, and it was a complete failure in every measurable way. I think it's awesome.`,
    // trackInstance - After it's loaded, we will place the audio data here
  },
  {
    name: 'do-wah-diddy',
    description: `My friend David Denison and I recorded this song in a living room with a Korg Electribe, a garbage laptop, and a broken logitech PC mic (we had to literally hold it together while using it), for fun. This is from about 2008. David is "rapping" and I'm singing. If you can't tell, Fallout Boy and that Gym Class Heroes song was popular at the time lol. This is the only place that this song has ever been published.`,
  },
]

async function selectSong(song: Song) {
  selectedSong.value?.trackInstance?.pause()

  loading.value = true
  selectedSong.value = null

  if (!song.trackInstance) {
    await initAudio()
    song.trackInstance = await createTrack(`/${name}.mp3`)
  }

  selectedSong.value = song
  loading.value = false
}
</script>

<template>
  <h1>MP3 Player Example</h1>
  <div class="track-list">
    <div class="select">
      <table>
        <tbody>
          <tr v-for="song in songs" :key="song.name" class="pointer" @click="selectSong(song)">
            <td>{{ titleCase(song.name) }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="description">
      <p v-if="selectedSong">
        {{ selectedSong.description }}
      </p>
      <p v-else>
        Select a track...
      </p>
    </div>
  </div>

  <!-- Mp3Player accepts a Track instance and emits 3 events -->
  <Mp3Player
    v-if="!loading && track"
    :track="track"
    @change-gain="(newGain) => track?.changeGainTo(newGain).from('inverseRatio')"
    @seek="(newPosition) => track?.seek(newPosition).from('ratio')"
    @toggle-play="track.isPlaying ? track.pause() : track.play()"
  />

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
