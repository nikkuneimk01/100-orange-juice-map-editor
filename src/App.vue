<template>
  <div id="app">
    <button v-for="panel in panelInfo" :key="panel.name" @click="ChangeCurrentPanelType(panel.source)">{{panel.name}}</button>
    <button>경로 설정</button>
    <table id="field">
      <tr v-for="i in mapSize*3" :key = i>
        <td v-for="j in mapSize*3" :key = j :ref="`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`" style="" @click="ChangeClickedPanelType(i, j)" @contextmenu.prevent="ChangeClickedPanelTypeNone(i, j)" :class="panelPosition[(i-1)%3][(j-1)%3]">
          <!-- {{`${(i-1)%3}, ${(j-1)%3}`}} -->
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  components: {
  },
  data() {
    return {
      mapSize: 11,
      panelInfo: [ {
          name: '보너스',
          source: 'mass_bonus0',
          color: '#ffc90e'
        }, {
          name: '드로우',
          source: 'mass_draw0',
          color: '#22b14c'
        }, {
          name: '드롭',
          source: 'mass_drop0',
          color: null,
        }, {
          name: '엔카운터',
          source: 'mass_encount0',
          color: '#eb1c24'
        }, {
          name: '무브',
          source: 'mass_move0',
          color: '#00a2e8'
        }, {
          name: '워프',
          source: 'mass_warp0',
          color: '#a349a4'
        }, {
          name: '체크',
          source: 'mass_check0',
          color: '#ff7f27'
        }, {
          name: '패널 제거',
          source: 'mass_none',
          color: '#000000'
        }
      ],
      currentPanelType: 'mass_none',
      // panelPosition: [
      //   [{ backgroundPosition: 'top left'}, { backgroundPosition: 'top center'}, { backgroundPosition: 'top right'}],
      //   [{ backgroundPosition: 'center left'}, { backgroundPosition: 'center center'}, { backgroundPosition: 'center right'}],
      //   [{ backgroundPosition: 'bottom left'}, { backgroundPosition: 'bottom center'}, { backgroundPosition: 'bottom right'}],
      // ]
      panelPosition: [
        ['top-left', 'top_center', 'top-right'],
        ['center-left', 'center-center', 'center-right'],
        ['bottom-left', 'bottom-center', 'bottom-right'],
      ]
    }
  },
  methods: {
    ChangeCurrentPanelType(panel) {
      // alert(panel);
      this.currentPanelType = panel;
    },
    ChangeClickedPanelType(ipos, jpos) {
      const row = parseInt((ipos-1) / 3);
      const col = parseInt((jpos-1) / 3);

      this.$refs[`cell_${row}_${col}`].forEach(cell => {
        cell.setAttribute('style', `background: url(/static/img/panels/${this.currentPanelType}.png);`);
      });
    },
    ChangeClickedPanelTypeNone(ipos, jpos) {
      const row = parseInt((ipos-1) / 3);
      const col = parseInt((jpos-1) / 3);

      this.$refs[`cell_${row}_${col}`].forEach(cell => {
        cell.setAttribute('style', `background: url(/static/img/panels/mass_none.png);`);
      });
    }
  },
  watch: {
    currentPanelType() {
      console.log(this.currentPanelType);
    }
  }
}
</script>

<style scoped>
  table#field {
    padding: 0;
    border: 1px solid #ff0000 !important;
    border-collapse:collapse
  }
  table#field td {
    width: 20px;
    height: 20px;
    background: url(/static/img/panels/mass_none.png);
    background-size: 300% !important;
    padding: 0;
  }
  td.top-left {
    background-position: top left !important;
  }
  .top_center {
    background-position: top center !important;
  }
  .top-right {
    background-position: top right !important;
  }
  .center-left {
    background-position: center left !important;
  }
  .center-center {
    background-position: center center !important;
  }
  .center-right {
    background-position: center right !important;
  }
  .bottom-left {
    background-position: bottom left !important;
  }
  .bottom-center {
    background-position: bottom center !important;
  }
  .bottom-right {
    background-position: bottom right !important;
  }
  table#field td:nth-child(3n) {
    border-right: 1px solid #cccccc;
  }
  table#field tr:nth-child(3n) {
    border-bottom: 1px solid #cccccc;
  }
</style>
