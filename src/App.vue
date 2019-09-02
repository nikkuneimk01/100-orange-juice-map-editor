<template>
  <div id="app">
    <!-- <button v-for="panel in panelInfo" :key="panel.name" @click="ChangeSeletedPanelType(panel)">{{panel.name}}</button>
    <button @click="isSetPath=true">경로 설정</button>
    <button @click="convertMap2Png()">이미지화</button>
    <button @click="cellSize++">셀 크기 +</button>
    <button @click="cellSize--">셀 크기 -</button> -->
    <div id="controlPanel">
      <table>
        <tr>
          <td>
            
          </td>
        </tr>
      </table>
    </div>
    <table id="field">
      <tr v-for="i in mapSize * 3" :key = i>
        <td v-for="j in mapSize * 3" :key = j :ref="`cell_${parseInt((i-1) / 3)}_${parseInt((j-1) / 3)}`" style="" @click="bindCellClickEvent(i, j)" @contextmenu.prevent="bindCellContextEvent(i, j)" :class="panelPosition[(i-1)%3][(j-1)%3]" :style="{width: cellSize+'px', height: cellSize + 'px !important'}">
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
</template>

<script>
import Jimp from 'jimp';
import { PNG } from 'pngjs';
import fs from 'fs';
import config from './config.json';

export default {
  components: {
  },
  data() {
    return {
      mapSize: 11,
      cellSize: 20,
      panelInfo: [ {
          name: '일반',
          source: 'mass_common0',
          color: config.colorInfo.common
        }, {
          name: '보너스',
          source: 'mass_bonus0',
          color: config.colorInfo.bonus
        }, {
          name: '더블 보너스',
          source: 'mass_bonusL2',
          color: config.colorInfo.bonusL
        }, {
          name: '드로우',
          source: 'mass_draw0',
          color: config.colorInfo.draw
        }, {
          name: '더블 드로우',
          source: 'mass_drawL2',
          color: config.colorInfo.drawL
        }, {
          name: '드롭',
          source: 'mass_drop0',
          color: config.colorInfo.drop
        }, {
          name: '더블 드롭',
          source: 'mass_dropL2',
          color: config.colorInfo.dropL
        }, {
          name: '엔카운터',
          source: 'mass_encount0',
          color: config.colorInfo.encount
        }, {
          name: '더블 엔카운터',
          source: 'mass_encountL2',
          color: config.colorInfo.encountL
        }, {
          name: '무브',
          source: 'mass_move0',
          color: config.colorInfo.move
        }, {
          name: '더블 무브',
          source: 'mass_moveL2',
          color: config.colorInfo.moveL
        }, {
          name: '워프',
          source: 'mass_warp0',
          color: config.colorInfo.warp
        }, {
          name: '워프 무브',
          source: 'mass_warpmove0',
          color: config.colorInfo.warpmove
        }, {
          name: '워프 더블 무브',
          source: 'mass_warpmoveL2',
          color: config.colorInfo.warpmoveL
        }, {
          name: '체크',
          source: 'mass_check0',
          color: config.colorInfo.check
        }, {
          name: '힐',
          source: 'mass_heal',
          color: config.colorInfo.heal
        }, {
          name: '더블 힐',
          source: 'mass_healL2',
          color: config.colorInfo.healL
        }, {
          name: '아이스',
          source: 'mass_ice0',
          color: config.colorInfo.ice
        }, {
          name: '미니게임',
          source: 'mass_minigame',
          color: config.colorInfo.minigame
        }
      ],
      currentPanelType: {
        name: '체크',
        source: 'mass_check0',
        color: '#ff7f27'
      },
      panelPosition: [
        ['top-left', 'top_center', 'top-right'],
        ['center-left', 'center-center', 'center-right'],
        ['bottom-left', 'bottom-center', 'bottom-right'],
      ],
      isSetPath: false,
      movePath: [],
      // struct
      // cell_row_col {
      //   comefrom~~, type
      // }
      fields: {},
    }
  },
  methods: {
    bindCellClickEvent(ipos, jpos) {
      if (this.isSetPath === false) {
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
        this.ChangeClickedPanelTypeNone(ipos, jpos);
      }
      // 이걸 쓰면 안대는데 무슨 버그지?
      this.$forceUpdate();
    },
    ChangeSeletedPanelType(panel) {
      this.isSetPath = false;
      this.movePath = [];
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
    async convertMap2Png(){
      // 맵 크기의 이미지를 만들고
      new Jimp(this.mapSize * 3, this.mapSize * 3, async (err, image) => {
        for(let i = 0; i < this.mapSize * 3; i++) {
          for(let j = 0; j < this.mapSize * 3; j++) {
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
        fs.writeFileSync('out2.png', buffer);

        // console.log(fs.createReadStream('in.png').pipe());

        // buff
        //   .pipe(new PNG({
        //     colorType: 2,
        //     bgColor: {
        //       red: 0,
        //       green: 0,
        //       blue: 0
        //     }
        //   }))
        //   .on('parsed', function() {
        //     this.pack().pipe(fs.createWriteStream('out.png'));
        //     console.log('fa')
        //   });
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
  table#field {
    padding: 0;
    border: 1px solid #cccccc;
    border-collapse:collapse;
    line-height: 0;
  }
  table#field td {
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
