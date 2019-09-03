<template>
  <div id="app" @click.middle="isSetPath = !isSetPath">
    <!-- <button v-for="panel in panelInfo" :key="panel.name" @click="ChangeSeletedPanelType(panel)">{{panel.name}}</button>
    <button @click="isSetPath=true">경로 설정</button>
    
    <button @click="cellSize++">셀 크기 +</button>
    <button @click="cellSize--">셀 크기 -</button> -->
    <!-- <button @click="convertMap2Png()">이미지화</button> -->
    <div id="control-panel">
      <div>
        <table id="panel-tools" :class="{ 'set-path-mode': isSetPath }" style="margin-bottom: 20px;">
          <tr v-for="(row, i) in panelLayout" :key="i">
            <td v-for="col in row" :key="col">
              <!-- {{col}} -->
              <img :src="`/static/img/panels/${panelInfo[col].source}.png`" width="60"
                @click="ChangeSeletedPanelType(panelInfo[col])" :title="col"
                style="cursor: pointer;"
                @mouseover="panelToolsHoverd" 
                @mouseleave="panelToolsLeaved" 
              > 
            </td>
          </tr>
        </table>
        <table id="other-tools" style="margin-bottom: 60px;">
          <tr>
            <td>불러오기</td>
            <td><img src="/static/img/tools/tool_filesave.svg" width="60" @click="convertMap2Png()"></td>
            <td></td>
            <!-- <td><img src="/static/img/tools/tool_arrow.svg" width="60" style="transform: rotate(180deg);" @click="mapResize(-1)"></td>
            <td><img src="/static/img/tools/tool_map.svg" width="60" style="cursor: default;" title="맵의 사이즈를 늘이거나 줄입니다."></td>
            <td><img src="/static/img/tools/tool_arrow.svg" width="60" @click="mapResize(1)"></td> -->
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td><img src="/static/img/tools/tool_arrow.svg" width="60" style="transform: rotate(180deg);"  @click="cellSize--"></td>
            <td><img src="/static/img/tools/tool_magnifying.svg" width="60" style="cursor: default;" title="셀을 확대하거나 축소합니다."></td>
            <td><img src="/static/img/tools/tool_arrow.svg" width="60" @click="cellSize++"></td>
          </tr>
        </table>
      </div>
    </div>
    <div id="map" v-if="isSelectMapSize">
      <table id="field">
        <tr v-for="i in mapSize[0] * 3" :key = i :style="{height: cellSize + 'px !important', 'min-height': cellSize + 'px !important'}">
          <td v-for="j in mapSize[1] * 3" :key = j :ref="`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`" @click="bindCellClickEvent(i, j)" @contextmenu.prevent="bindCellContextEvent(i, j)" :class="panelPosition[(i-1)%3][(j-1)%3]" :style="{'min-width': cellSize+'px', height: cellSize + 'px !important',  'min-height': cellSize + 'px !important'}">
            <!-- up arrow -->
            <img src="/static/img/panels/mass_arrow.png" :style="{width: cellSize+'px', transform: 'rotate(0deg)'}" v-if="(i-1)%3 === 2 && (j-1)%3 === 1 && fields[`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`] && fields[`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`].comefromDown === true">
            <!-- right arrow -->
            <img src="/static/img/panels/mass_arrow.png" :style="{width: cellSize+'px', transform: 'rotate(90deg)'}" v-if="(i-1)%3 === 1 && (j-1)%3 === 0 && fields[`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`] && fields[`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`].comefromLeft === true">
            <!-- down arrow -->
            <img src="/static/img/panels/mass_arrow.png" :style="{width: cellSize+'px', transform: 'rotate(180deg)'}" v-if="(i-1)%3 === 0 && (j-1)%3 === 1 && fields[`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`] && fields[`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`].comefromUp === true">
            <!-- left arrow -->
            <img src="/static/img/panels/mass_arrow.png" :style="{width: cellSize+'px', transform: 'rotate(-90deg)'}" v-if="(i-1)%3 === 1 && (j-1)%3 === 2 && fields[`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`] && fields[`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`].comefromRight === true">
          </td>
        </tr>
      </table>
    </div>
    <div id="map-size-select" v-else style="width: 610px;">
      <div id="select-button" style="width: 42%; float: left;">
        <ul class="gallog_menu" style="margin-top: 0px;">
          <li class="home" v-for="(item, i) in config.mapInfo" :key="`${item.size[0]}X${item.size[1]}`" @click="selectButtonIdx = i; mapList = item.maps; mapSize = item.size;" :class="{on: selectButtonIdx === i}">
            <span>{{`${item.size[1]}X${item.size[0]} 맵`}}</span>
          </li>
        </ul>
      </div>
      <div class="info_contbox">
        <div class="info_cont">
          <strong class="tit">매니저</strong>
          <p class="cont">
            <span class="mng_nick">ㅇㅇ(223.38)</span>
          </p>
        </div>
        <div class="info_cont">
          <strong class="tit">부매니저</strong>
          <p class="cont">
            <span class="mng_nick" v-for="item in mapList" :key="item[0]">{{item[0]}}</span>
          </p>
        </div>
        <div class="info_cont">
          <strong class="tit">개설일</strong>
          <p class="cont">2077-12-25</p>
        </div>
      </div>
      <div style="clear: both; height: 0; overflow: hidden;"></div>
      <button id="select-size" @click="isSelectMapSize = true;">선택</button>
    </div>
    
  </div>
</template>

<script>
import Jimp from 'jimp';
import { PNG } from 'pngjs';
import fs from 'fs';
import electron from 'electron';
import config from './config.json';

const { dialog } = electron.remote;

export default {
  components: {
  },
  data() {
    return {
      isSelectMapSize: false,
      selectButtonIdx: null,
      config,
      mapList: [[ '없음', 'none' ]],
      mapSize: [ 11, 11 ],
      cellSize: 20,
      panelLayout: [
        [ '일반', '보너스', '드로우', '엔카운터', '드롭', '무브', '워프', '워프 무브', '힐', '아이스'],
        [ '체크',   '더블 보너스', '더블 드로우', '더블 엔카운터', '더블 드롭', '더블 무브', '일반', '워프 더블 무브', '더블 힐', '미니게임']
      ],
      panelInfo: {
        '일반': {
          source: 'mass_common0',
          color: config.colorInfo.common
        },
        '보너스': {
          source: 'mass_bonus0',
          color: config.colorInfo.bonus
        }, 
        '더블 보너스': {
          source: 'mass_bonusL2',
          color: config.colorInfo.bonusL
        },
        '드로우': {
          source: 'mass_draw0',
          color: config.colorInfo.draw
        }, 
        '더블 드로우': {
          source: 'mass_drawL2',
          color: config.colorInfo.drawL
        }, 
        '드롭': {
          source: 'mass_drop0',
          color: config.colorInfo.drop
        }, 
        '더블 드롭': {
          source: 'mass_dropL2',
          color: config.colorInfo.dropL
        }, 
        '엔카운터': {
          source: 'mass_encount0',
          color: config.colorInfo.encount
        },
        '더블 엔카운터': {
          source: 'mass_encountL2',
          color: config.colorInfo.encountL
        }, 
        '무브': {
          source: 'mass_move0',
          color: config.colorInfo.move
        },
        '더블 무브': {
          source: 'mass_moveL2',
          color: config.colorInfo.moveL
        },
        '워프': {
          source: 'mass_warp0',
          color: config.colorInfo.warp
        },
        '워프 무브': {
          source: 'mass_warpmove0',
          color: config.colorInfo.warpmove
        },
        '워프 더블 무브': {
          source: 'mass_warpmoveL2',
          color: config.colorInfo.warpmoveL
        },
        '체크': {
          source: 'mass_check0',
          color: config.colorInfo.check
        },
        '힐': {
          source: 'mass_heal',
          color: config.colorInfo.heal
        },
        '더블 힐': {
          source: 'mass_healL2',
          color: config.colorInfo.healL
        },
        '아이스': {
          source: 'mass_ice0',
          color: config.colorInfo.ice
        }, 
        '미니게임': {
          source: 'mass_minigame',
          color: config.colorInfo.minigame
        }
      },
      currentPanelType: {
        source: 'mass_common0',
        color: config.colorInfo.common
      },
      panelPosition: [
        ['top-left', 'top_center', 'top-right'],
        ['center-left', 'center-center', 'center-right'],
        ['bottom-left', 'bottom-center', 'bottom-right'],
      ],
      isSetPath: false,
      movePath: [],
      flushPath: [],
      // struct
      // cell_row_col {
      //   comefrom~~, type
      // }
      fields: {},
    }
  },
  methods: {
    // mapResize(cost){
    //   this.mapSize += cost;
    //   this.$forceUpdate();
    // },
    addStyle(styleName, value, e){
      e.target.style[styleName] = value;
    },
    panelToolsHoverd(e){
      e.target.style.opacity = 0.2;
    },
    panelToolsLeaved(e){
      e.target.style.opacity = 1;
    },
    bindCellClickEvent(ipos, jpos) {
      if (this.isSetPath === false) {
        this.movePath = [];
        this.flushPath = [];
        this.ChangeClickedPanelType(ipos, jpos);
      }
      else {
        this.setPath(ipos, jpos);
      }
      // 이걸 쓰면 안대는데 무슨 버그지?
      this.$forceUpdate();
    },
    bindCellContextEvent(ipos, jpos) {
      if (this.isSetPath === false) {
        this.movePath = [];
        this.flushPath = [];
        this.ChangeClickedPanelTypeNone(ipos, jpos);
      }
      else {
        this.deletePath(ipos, jpos);
      }
      // 이걸 쓰면 안대는데 무슨 버그지?
      this.$forceUpdate();
    },
    ChangeSeletedPanelType(panel) {
      this.currentPanelType = panel;
    },
    ChangeClickedPanelType(ipos, jpos) {
      // 클릭한 셀의 좌표를 필드 좌표로 변환
      const row = parseInt((ipos-1) / 3);
      const col = parseInt((jpos-1) / 3);

      // 표 돔조작
      this.$refs[`cell_${row}_${col}`].forEach(cell => {
        cell.setAttribute('style', `background: url(/static/img/panels/${this.currentPanelType.source}.png);`);
      });

      // fields 변경
      if(!this.fields[`cell_${row}_${col}`]) this.fields[`cell_${row}_${col}`] = {};
      this.fields[`cell_${row}_${col}`].row = row;
      this.fields[`cell_${row}_${col}`].col = col;
      this.fields[`cell_${row}_${col}`].color = this.currentPanelType.color;
      console.log(this.fields);
    },
    ChangeClickedPanelTypeNone(ipos, jpos) {
      // 클릭한 셀의 좌표를 필드 좌표로 변환
      const row = parseInt((ipos-1) / 3);
      const col = parseInt((jpos-1) / 3);

      // 표 돔조작
      this.$refs[`cell_${row}_${col}`].forEach(cell => {
        cell.setAttribute('style', `background: url(/static/img/panels/mass_none.png);`);
      });

      // fields 변경
      if(!this.fields[`cell_${row}_${col}`]) this.fields[`cell_${row}_${col}`] = {};
      this.fields[`cell_${row}_${col}`].row = row;
      this.fields[`cell_${row}_${col}`].col = col;
      this.fields[`cell_${row}_${col}`].color = config.colorInfo.none;

      // 클릭 패널로 오는 연관된 경로 제거
      this.fields[`cell_${row}_${col}`].comefromUp = false;
      this.fields[`cell_${row}_${col}`].comefromDown = false;
      this.fields[`cell_${row}_${col}`].comefromLeft = false;
      this.fields[`cell_${row}_${col}`].comefromRight = false;
      // 클릭 패널에서 가는 연관된 경로 제거
      if(this.fields[`cell_${row-1}_${col}`]) this.fields[`cell_${row-1}_${col}`].comefromDown = false;
      if(this.fields[`cell_${row+1}_${col}`]) this.fields[`cell_${row+1}_${col}`].comefromUp = false;
      if(this.fields[`cell_${row}_${col-1}`]) this.fields[`cell_${row}_${col-1}`].comefromLeft = false;
      if(this.fields[`cell_${row}_${col+1}`]) this.fields[`cell_${row}_${col+1}`].comefromRight = false;

      console.log(this.fields);
    },
    setPath(ipos, jpos) {
      const direction = [{drow: -1, dcol: 0}, {drow: 0, dcol: 1}, {drow: 1, dcol: 0}, {drow: 0, dcol: -1}];

      const row = parseInt((ipos-1) / 3);
      const col = parseInt((jpos-1) / 3);

      this.flushPath = [];

      // 클릭한 패널이 빈 공간이라면 무시
      if(!this.fields[`cell_${row}_${col}`] || this.fields[`cell_${row}_${col}`].color === config.colorInfo.none) {
        return;
      }

      this.movePath.push({row, col});
      console.log('pushed');
      if (this.movePath.length >= 2) {
        if(!direction.some((item, i) => {
          // 클릭한 두 패널이 인접해있는지 확인
          if(this.movePath[1].row - this.movePath[0].row === item.drow && this.movePath[1].col - this.movePath[0].col === item.dcol) {
            console.log(i);

            // 아래쪽에서 현재 패널로
            if(i === 0) this.fields[`cell_${this.movePath[1].row}_${this.movePath[1].col}`].comefromDown = true;
            // 왼쪽에서 현재 패널로
            else if(i === 1) this.fields[`cell_${this.movePath[1].row}_${this.movePath[1].col}`].comefromLeft = true;
            // 위쪽에서 현재 패널로
            else if(i === 2) this.fields[`cell_${this.movePath[1].row}_${this.movePath[1].col}`].comefromUp = true;
            // 오른쪽에서 현재 패널로
            else if(i === 3) this.fields[`cell_${this.movePath[1].row}_${this.movePath[1].col}`].comefromRight = true;

            this.movePath.shift();
            return true;
          }
          else return false;
        })) {
          console.log('fuck');
          this.movePath.shift();
        }
      }
    },
    deletePath(ipos, jpos) {
      const direction = [{drow: -1, dcol: 0}, {drow: 0, dcol: 1}, {drow: 1, dcol: 0}, {drow: 0, dcol: -1}];

      const row = parseInt((ipos-1) / 3);
      const col = parseInt((jpos-1) / 3);

      this.movePath = [];

      // 클릭한 패널이 빈 공간이라면 무시
      if(!this.fields[`cell_${row}_${col}`] || this.fields[`cell_${row}_${col}`].color === config.colorInfo.none) {
        return;
      }

      this.flushPath.push({row, col});
      console.log('pushed');
      if (this.flushPath.length >= 2) {
        if(!direction.some((item, i) => {
          // 클릭한 두 패널이 인접해있는지 확인
          if(this.flushPath[1].row - this.flushPath[0].row === item.drow && this.flushPath[1].col - this.flushPath[0].col === item.dcol) {
            console.log(i);

            // 아래쪽에서 현재 패널로
            if(i === 0) this.fields[`cell_${this.flushPath[1].row}_${this.flushPath[1].col}`].comefromDown = false;
            // 왼쪽에서 현재 패널로
            else if(i === 1) this.fields[`cell_${this.flushPath[1].row}_${this.flushPath[1].col}`].comefromLeft = false;
            // 위쪽에서 현재 패널로
            else if(i === 2) this.fields[`cell_${this.flushPath[1].row}_${this.flushPath[1].col}`].comefromUp = false;
            // 오른쪽에서 현재 패널로
            else if(i === 3) this.fields[`cell_${this.flushPath[1].row}_${this.flushPath[1].col}`].comefromRight = false;

            this.flushPath.shift();
            return true;
          }
          else return false;
        })) {
          console.log('fuck');
          this.flushPath.shift();
        }
      }
    },
    async convertMap2Png(){
      // 맵 크기의 이미지를 만들고
      new Jimp(this.mapSize[1] * 3, this.mapSize[0] * 3, async (err, image) => {
        for(let i = 0; i < this.mapSize[0] * 3; i++) {
          for(let j = 0; j < this.mapSize[1] * 3; j++) {
            image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.none), j, i);
            // console.log(i, j);
          }
        }
        //
        // 모든 저장된 패널 정보를 순회
        Object.keys(this.fields).map((key) => {
          // 빈 패널은 거른다
          if(this.fields[key].color === config.colorInfo.none)  return;
          // key값으로 row col 조회
          const splitedKey = key.split('_');
          const row = parseInt(splitedKey[1]);
          const col = parseInt(splitedKey[2]);

          // 한 패널당 행렬 3픽셀 픽셀 정보로
          for(let i = row * 3; i < (row + 1) * 3; i++) {
            for(let j = col * 3; j < (col + 1) * 3; j++) {
              image.setPixelColor(Jimp.cssColorToHex(this.fields[key].color), j, i);
              console.log(i, j);
            }
          }

          // 패널 이동 방향 표시
          if(this.fields[key].comefromUp) image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.frontDirection), col * 3 + 1, row * 3);
          else if(this.fields[`cell_${row-1}_${col}`] && this.fields[`cell_${row-1}_${col}`].color !== config.colorInfo.none) image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.backDirection), col * 3 + 1, row * 3);

          if(this.fields[key].comefromDown) image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.frontDirection), col * 3 + 1, row * 3 + 2);
          else if(this.fields[`cell_${row+1}_${col}`] && this.fields[`cell_${row+1}_${col}`].color !== config.colorInfo.none) image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.backDirection), col * 3 + 1, row * 3 + 2);

          if(this.fields[key].comefromLeft) image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.frontDirection), col * 3, row * 3 + 1);
          else if(this.fields[`cell_${row}_${col-1}`] && this.fields[`cell_${row}_${col-1}`].color !== config.colorInfo.none) image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.backDirection), col * 3, row * 3 + 1);

          if(this.fields[key].comefromRight) image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.frontDirection), col * 3 + 2, row * 3 + 1);
          else if(this.fields[`cell_${row}_${col+1}`] && this.fields[`cell_${row}_${col+1}`].color !== config.colorInfo.none) image.setPixelColor(Jimp.cssColorToHex(config.colorInfo.backDirection), col * 3 + 2, row * 3 + 1);
        });

        const buff = await image.getBufferAsync(Jimp.MIME_PNG);
        // console.log(buff);
        var png = PNG.sync.read(buff);
        var options = { colorType: 2 };
        var buffer = PNG.sync.write(png, options);

        const filePath = await dialog.showSaveDialogSync({ filters: [ { name: '24비트 PNG (*.png)', extensions: [ 'png' ] } ] });
        console.log(filePath);
        if(filePath)  fs.writeFileSync(filePath, buffer);
      });
    },
    deepCompare () {
      var i, l, leftChain, rightChain;

      function compare2Objects (x, y) {
        var p;

        // remember that NaN === NaN returns false
        // and isNaN(undefined) returns true
        if (isNaN(x) && isNaN(y) && typeof x === 'number' && typeof y === 'number') {
          return true;
        }

        // Compare primitives and functions.
        // Check if both arguments link to the same object.
        // Especially useful on the step where we compare prototypes
        if (x === y) {
          return true;
        }

        // Works in case when functions are created in constructor.
        // Comparing dates is a common scenario. Another built-ins?
        // We can even handle functions passed across iframes
        if ((typeof x === 'function' && typeof y === 'function') ||
          (x instanceof Date && y instanceof Date) ||
          (x instanceof RegExp && y instanceof RegExp) ||
          (x instanceof String && y instanceof String) ||
          (x instanceof Number && y instanceof Number)) {
            return x.toString() === y.toString();
        }

        // At last checking prototypes as good as we can
        if (!(x instanceof Object && y instanceof Object)) {
          return false;
        }

        if (x.isPrototypeOf(y) || y.isPrototypeOf(x)) {
          return false;
        }

        if (x.constructor !== y.constructor) {
          return false;
        }

        if (x.prototype !== y.prototype) {
          return false;
        }

        // Check for infinitive linking loops
        if (leftChain.indexOf(x) > -1 || rightChain.indexOf(y) > -1) {
          return false;
        }

        // Quick checking of one object being a subset of another.
        // todo: cache the structure of arguments[0] for performance
        for (p in y) {
          if (y.hasOwnProperty(p) !== x.hasOwnProperty(p)) {
            return false;
          }
          else if (typeof y[p] !== typeof x[p]) {
            return false;
          }
        }

        for (p in x) {
          if (y.hasOwnProperty(p) !== x.hasOwnProperty(p)) {
            return false;
          }
          else if (typeof y[p] !== typeof x[p]) {
            return false;
          }

          switch (typeof (x[p])) {
            case 'object':
            case 'function':
              leftChain.push(x);
              rightChain.push(y);

              if (!compare2Objects (x[p], y[p])) {
                return false;
              }

              leftChain.pop();
              rightChain.pop();
              break;

            default:
              if (x[p] !== y[p]) {
                return false;
              }
              break;
          }
        }

        return true;
      }

      if (arguments.length < 1) {
        return true; //Die silently? Don't know how to handle such case, please help...
        // throw "Need two or more arguments to compare";
      }

      for (i = 1, l = arguments.length; i < l; i++) {

        leftChain = []; //Todo: this can be cached
        rightChain = [];

        if (!compare2Objects(arguments[0], arguments[i])) {
          return false;
        }
      }

      return true;
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
  table#field, table#panel-tools, table#other-tools {
    padding: 0;
    border: 1px solid #cccccc;
    border-collapse:collapse;
    line-height: 0;
  }
  table#panel-tools td, table#other-tools td {
    padding: 0;
    border: 1px solid #cccccc;
  }
  table#field td {
    background: url(/static/img/panels/mass_none.png);
    background-size: 300% !important;
    padding: 0;
  }
  table#other-tools td {
    min-width: 60px;
    width: 60px;
    height: 60px;
    border: 1px solid #cccccc;
    color: white;
  }
  table#other-tools img {
    cursor: pointer;
  }
  table.set-path-mode {
    opacity: 0.1;
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
  button#select-size {
    width: 610px !important;
    background: #fff;
  }

  /* 디씨 CSS */
  .gallog_menu {
    overflow: hidden;
    margin-bottom: 32px;
    list-style: none;
  }
  /* 직접 추가 */
  ul.gallog_menu {
    padding-left: 0;
  }
  /* 직접 추가 끝 */
  .gallog_menu li {
    position: relative;
    float: left;
    margin-left: 2px;
  }
  .gallog_menu li:nth-child(2n+1) {
    margin-left: 20px;
  }
  .gallog_menu li.on span {
    background: #4a57a8;
    color: #fff;
    text-shadow: 0px -1px #343d8e;
  }
  .gallog_menu li span, button#select-size {
    display: block;
    width: 97px;
    height: 35px;
    padding-right: 1px;
    line-height: 37px;
    border: 1px #4a57a8 solid;
    border-radius: 2px;
    font-family: Dotum,'돋움';
    font-size: 14px;
    font-weight: bold;
    text-align: center;
    cursor: pointer;
  }
  /* 직접 입력 */
  button#select-size:hover {
    background: #4a57a8;
    color: #fff;
    text-shadow: 0px -1px #343d8e;
  }
  /* 직접 입력 끝 */
  .info_contbox {
    overflow-y: auto;
    position: relative;
    width: 54%;
    float: left;
    height: 104px;
    margin-top: -3px;
    margin-left: 4px;
    z-index: 1;
  }
  .info_cont {
    overflow: hidden;
    line-height: 22px;
  }
  .info_cont .tit {
    display: block;
    float: left;
    width: 73px;
  }
  .info_cont .cont {
    float: left;
    text-align: left;
    width: auto;
    padding: 0;
    line-height: 20px;
    margin-top: 0 !important;
    margin-bottom: 0 !important;
  }
  .mng_nick {
    display: block;
  }
  .info_contbox {
    font-size: 12px;
    font-family: Dotum,'돋움',Helvetica,"Apple SD Gothic Neo",sans-serif;
  }
</style>
