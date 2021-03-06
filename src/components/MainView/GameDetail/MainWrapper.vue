<template>
  <div :class="$style.container">
    <div :class="$style.button">
      <play-button
        :gameInList="gameInList"
        :game="game"
        @createList="createList"
      />
    </div>
    <div :class="$style.Link">
      <link-c
        v-for="(link, i) in links" :key="i"
        :url="link.url"
        :title="link.title"
        :class="$style.item"
      />
    </div>
    <game-info :class="$style.gameInfo" :creators="creators" />
    <score-c :class="$style.score" :score="score "/>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, computed, ref, onMounted } from '@vue/composition-api';
import LinkC from './Link.vue'
import PlayButton from './PlayButton.vue'
import GameInfo, { CreatorInfo } from './GameInfo.vue'
import ScoreC, { Score } from './Score.vue'
import { Game, ListGame } from '../../../types/root';
import useScraping from '../../use/useScraping'
import { number } from 'yargs';

export default defineComponent({
  name: 'MainWrapper',
  props: {
    game: {
      type: Object as PropType<Game>,
      required: true
    },
    gameInList :{
      type: Object as PropType<Record<number, ListGame>>,
      required: true
    },
    seiya: {
      type: Object as PropType<{createdNow: number, games: {name: string, url: string}[]}>,
      required: true
    }
  },
  components: {
    LinkC,
    PlayButton,
    GameInfo,
    ScoreC,
  },
  setup(props, context) {
    const { getSeiyaURL } = useScraping()
    const links = computed(() => [
      { title: 'OHP', url: props.game.officialHomePage },
      { title: 'ErogameSpace', url: `https://erogamescape.dyndns.org/~ap2/ero/toukei_kaiseki/game.php?game=${props.game.id}` },
      { title: '誠也の部屋', url: getSeiyaURL(props.game.name, props.seiya.games) }
    ])
    const score = computed<Score>(() => ({
      median: props.game.median,
      average: props.game.average,
      count: props.game.count
    }))
    const creators = computed<CreatorInfo>(() => ({
      gengas: props.game.gengas,
      sinarios: props.game.sinarios,
      seiyus: props.game.seiyus
    }))
    const createList = () => {
      context.emit('createList')
    }
    return { links, score, creators, createList }
  }
});
</script>

<style lang="scss" module>
.container {
  position: relative;
  display: flex;
  flex-direction: column;
}
.button {
  margin-top: 16px;
}
.Link {
  display: flex;
}
.item {
  margin-top: 16px;
  margin-right: 16px;
}
.gameInfo {
  margin-top: 16px;
  margin-bottom: 8px;
}
.score {
  position: absolute;
  margin-right: 24px;
  right: 0;
  top: 0;
}
</style>
