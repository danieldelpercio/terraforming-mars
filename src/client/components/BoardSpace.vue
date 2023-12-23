<template>
  <div :class="getMainClass()" :data_space_id="space.id" :style="getSpaceMargin()" v-if="+this.space.id >= 100">
    <board-space-tile
      :space="space"
      :aresExtension="aresExtension"
      :tileView="tileView"
    ></board-space-tile>
    <div class="board-space-text" v-if="text" v-i18n>{{ text }}</div>
    <bonus :bonus="space.bonus" v-if="showBonus"></bonus>
    <template v-if="tileView === 'coords'">
      <div class="board-space-coords">({{ space.y }}, {{ space.x }}) ({{ space.id }})</div>
    </template>
    <template v-if="tileView === 'show'">
      <div :class="'board-cube board-cube--'+space.color" v-if="space.color !== undefined"></div>
      <template v-if="space.gagarin !== undefined">
        <div v-if="space.gagarin === 0" class='gagarin'></div>
        <div v-else class='gagarin visited'></div>
      </template>
      <template v-if="space.cathedral === true">
        <div class='board-cube--cathedral'></div>
      </template>
      <template v-if="space.nomads === true">
        <div class='board-cube--nomad'></div>
      </template>
      <template v-if="space.undergroundResources !== undefined">
        <underground-resources
          :space="space"
          :tileView="tileView"
        ></underground-resources>
      </template>
    </template>
    </div>
</template>

<script lang="ts">

import Vue from 'vue';
import Bonus from '@/client/components/Bonus.vue';
import BoardSpaceTile from '@/client/components/board/BoardSpaceTile.vue';
import UndergroundResources from '@/client/components/board/UndergroundResources.vue';
import {TileView} from '@/client/components/board/TileView';
import {SpaceModel} from '@/common/models/SpaceModel';

export default Vue.extend({
  name: 'board-space',
  props: {
    space: {
      type: Object as () => SpaceModel,
    },
    text: {
      type: String,
    },
    aresExtension: {
      type: Boolean,
    },
    tileView: {
      type: String as () => TileView,
    },
  },
  data() {
    return {};
  },
  components: {
    'bonus': Bonus,
    'board-space-tile': BoardSpaceTile,
    'underground-resources': UndergroundResources,
  },
  methods: {
    getMainClass(): string {
      const mainClass = 'board-space-cont';
      const selectableClass = 'board-space-selectable';

      if (+this.space.id < 100) {
        return `${mainClass} ${this.space.id.toString()} ${selectableClass}`;
      }

      return `${mainClass} ${selectableClass}`;
    },
    // Calculate top and left margin values to correctly place
    // the hexagon tiles on the planet
    getSpaceMargin(): object {
      // Initial position
      const xStart = 6;
      const yStart = 14;

      const xInterval = 11;
      const yInterval = 9;

      const xOffset = 5.5;

      if (this.space.yRelativeToEquator === undefined) {
        console.error(`yRelativeToEquator is undefined on space with id ${this.space.id}`);
        return {};
      }

      const rowOffset = this.space.yRelativeToEquator * xOffset;
      const left = xStart + (this.space.x * xInterval) - rowOffset;
      const top = yStart + (this.space.y * yInterval);

      const styleObject = {
        left: `${left}%`,
        top: `${top}%`,
      };

      return styleObject;
    },
  },
  computed: {
    showBonus(): boolean {
      return this.space.tileType === undefined || this.tileView === 'hide';
    },
  },
});

</script>

