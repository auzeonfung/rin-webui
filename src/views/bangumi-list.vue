<style scoped lang="less">
  @import "../less/colors.less";
  ul,
  li,
  p {
    margin: 0;
    padding: 0;
    list-style-type: none
  }
  
  .rin-column {
    position: relative;
    width: 65px;
    box-sizing: border-box;
    color: #fff;
    background: @color-primary-1;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    flex: 1 0 auto;
  }
  
  .rin-text-overflow {
    /*文字截断*/
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  
  .rin-week {
    overflow-y: hidden;
    .rin-week-item {
      flex: 1 0 auto;
      background: #eee;
      .rin-week-title {
        position: relative;
        width: 46px;
        box-sizing: border-box;
        background: #ddd;
        color: #fff;
        display: flex;
        align-items: center;
        justify-content: center;
        transition: all .2s linear;
        cursor: pointer;
      }
      .title-bgc0 {
        background: #f44336;
      }
      .title-bgc1 {
        background: #e91e63;
      }
      .title-bgc2 {
        background: #9c27b0;
      }
      .title-bgc3 {
        background: #2196f3;
      }
      .title-bgc4 {
        background: #00bcd4;
      }
      .title-bgc5 {
        background: #4caf50;
      }
      .title-bgc6 {
        background: #ff5722;
      }
      .rin-week-content {
        flex-direction: column;
        flex-wrap: wrap;
        box-sizing: border-box;
        padding: 0 10px;
        width: 360px;
        transition: all .2s linear;
        overflow: hidden;
        &.off {
          width: 1px!important;
          padding: 0;
          margin-left: -1px;
        }
        .item {
          position: relative;
          background: #fff;
          overflow: hidden;
          padding: 10px;
          margin: 10px;
          margin-bottom: 0;
          border: 1px solid #E0E0E0;
          box-shadow: 0 2px 1px rgba(0, 0, 0, 0.04);
          border-radius: 5px;
          width: 300px;
          transition: all .3s linear;
          &:hover {
            border-color: #c3c3c3;
            transform: translateX(3px);
            box-shadow: 0 4px 5px rgba(0, 0, 0, 0.07);
            .acgdb {
              right: -30px;
            }
          }
          .preview {
            width: 60px;
            height: 60px;
            float: left;
            cursor: pointer;
            img {
              width: 100%;
              height: 100%;
              border-radius: 5px;
              font-size: 12px;
            }
          }
          .content {
            padding-left: 68px;
            font-size: 14px;
            height: 60px;
            line-height: 20px;
            .title a {
              color: #333;
            }
            .date {
              color: #9C9C9C;
              font-size: 12px;
            }
          }
          .rin-tag {
            margin-bottom: -2px;
          }
          .acgdb {
            position: absolute;
            width: 62px;
            height: 21px;
            top: -10px;
            background: #681558;
            padding: 5px;
            color: #fff;
            font-size: 28px;
            right: -70px;
            line-height: 32px;
            text-align: center;
            transition: right .2s linear;
            transform: rotate(45deg);
          }
        }
      }
    }
  }
  
  .rin-datatip {
    opacity: 0;
    position: absolute;
    top: -999em;
    left: 0;
    background: #681558;
    color: #fff;
    padding: 5px 8px;
    border-radius: 5px;
    transform: translateY(10px);
    transition: transform .3s linear, opacity .3s linear;
    box-shadow: 0px 0px 0px 2px #fff, 0px 0px 0px 4px #681558;
    &.on {
      opacity: 1;
      transform: translateY(0);
    }
  }
  
  .thisweek {
    display: inline-block;
    font-size: 14px;
    width: 20px;
    height: 20px;
    color: #ddd;
    border-radius: 50%;
    border: 1px solid #ddd;
    text-align: center;
  }
  
  .week-mask {
    position: absolute;
    font-size: 26px;
    left: 0;
    top: -10px;
    color: #fff;
    transform: rotate(-20deg);
    color: rgba(255, 255, 255, 0.2);
  }
  
  .rin-loader {
    margin-right: 128px;
    margin-left: 65px;
    transition: opacity .6s ease;
  }
</style>
<template>
  <div id="rin-main" class="rin-row" style="width: calc(100% - 128px);">

    <div class="rin-week rin-row" id="rin-week">
      <div is="rin-loader" :progress="progress" v-show="busy" transition="rin-fade"></div>

      <div class="rin-column" v-on:click.stop="allSwitch">
        <span class="rin-vertical">{{$t('Bangumi List')}}</span>
      </div>

      <div class="rin-week-item rin-row" v-for="(item, index) in datas">
        <div class="rin-week-title" :class="`title-bgc${index}`" @:click.stop="Switch(index)">
          <span class="rin-vertical">{{$t(locale.week[index]) | week}}</span>
          <span class="week-mask">{{week[index]}}</span>
        </div>
        <div class="rin-week-content rin-row" :class="{'off':isOff[index]}">

          <div class="item" v-for="d in item">
            <div class="preview">
              <img v-bind:src="serverUrl + d.icon" :alt="d.name">
            </div>
            <div class="content">
              <p class="title rin-text-overflow">
                <router-link :to="{name: 'search', params: {key: d.tag_id}}">{{d.tag.name}}</router-link>
                <!--<a v-link="'/search/`' + d.tag_id + '`'" :title="$t(d.tag)"></a>-->
              </p>
              <p class="date rin-text-overflow" :title="d.credit">{{d.credit}}</p>
              <p class="date">
                {{$t('On air')}}: {{d.startDate | handleDate('LL')}}
                <span class="thisweek" v-if="d.showOn === thisWeek">
                  今
                </span>
              </p>
            </div>
            <div class="rin-tag">
              <router-link class="haspic" v-for="t in d.team" :to="{name: 'search', params: {key: d.tag_id}}">
                <img src="../assets/akarin.jpg" v-if="!t.icon" />
                <img
                v-if="t.icon"
                v-bind:src="serverUrl + t.icon" />
                <span>{{t.tag.name}}</span>
              </router-link>
            </div>
            <a class="acgdb" :href="`http://acgdb.com/${d.acgdb_id?d.acgdb_id:''}`" target="_black" v-on:mouseover="showTip" v-on:mouseout="hideTip">
              ···
            </a>
          </div>

        </div>
      </div>
      <div class="rin-datatip" id="tip">
        {{$t('Read more on ACGDB')}}
      </div>
    </div>

  </div>
</template>

<script>
import RLoader from '../components/rin-loader';
import moment from 'moment';

export default {
  name: 'BangumiList',
  data() {
    return {
      progress: 0,
      locale: {
        week: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
      },
      datas: [],
      isOff: [true, true, true, true, true, true, true, true],
      thisWeek: new Date().getDay(),
      week: ['日', '月', '火', '水', '木', '金', '土'],
      busy: true,
      serverUrl: 'https://bangumi.moe/',
    };
  },
  filters: {
    date: require('../filters/dateFormat.js'),
    week(value) {
      const str = /（日）|（月）|（火）|（水）|（木）|（金）|（土）/;
      return value.replace(str, '');
    },
    handleDate(value, formatR) {
      let format = formatR;
      // 判断是否为三个月前的
      if ((new Date().getTime() - new Date(value).getTime()) >= 3 * 30 * 24 * 60 * 60 * 1000) {
        format = 'HH:mm';
      }
      return moment(value).format(format);
    },
  },
  components: {
    'rin-loader': RLoader,
  },
  methods: {
    // 全部列表切换
    allSwitch() {
      // 如果某个列表处于关闭状态，则全部打开。反之，全部关闭
      if (this.isOff.indexOf(true)) {
        for (let i = 0; i < 7; i++) {
          // this.isOff.$set(i, true);
          this.isOff[i] = true;
        }
      } else {
        for (let i = 0; i < 7; i++) {
          // this.isOff.$set(i, false);
          this.isOff[i] = false;
        }
      }
    },
    // 单个列表切换
    Switch(index) {
      // this.isOff.$set(index, !this.isOff[index]);
      this.isOff[index] = !this.isOff[index];
    },
    setWidth() {
      const allItem = document.querySelectorAll('.rin-week-content');
      for (const item of allItem) {
        item.style.width = `${(item.scrollWidth > 400 ? item.scrollWidth + 20 : item.scrollWidth)}px`;
      }
    },
    getData() {
      this.$http.get('https://bangumi.moe/api/v2/bangumi/current').then(
        resp => {
          this.progress = 100;
          const d = resp.data;

          const result = [];
          const teams = d.working_teams;
          const data = d.bangumis;

          for (const i in data) {
            // 往番剧增加字幕组
            if (teams[data[i].tag_id]) {
              data[i].team = teams[data[i].tag_id];
            }

            // 按星期生成数组
            if (result[data[i].showOn]) {
              result[data[i].showOn].push(data[i]);
            } else {
              result[data[i].showOn] = [data[i]];
            }
          }

          this.datas = result;
          this.busy = false; // 关闭加载
          setTimeout(this.setWidth, 100);
        });
    },
    showTip(e) {
      const tip = document.getElementById('tip');
      tip.className = 'rin-datatip on';
      tip.style.left = `${e.pageX - (tip.offsetWidth / 2)}px`;
      tip.style.top = `${e.pageY + 20}px`;
    },
    hideTip() {
      const tip = document.getElementById('tip');
      tip.className = 'rin-datatip';
      setTimeout(() => {
        tip.style.left = '-999px';
      }, 300);
    },
  },
  mounted() {
    console.log('[BangumiList]loaded');

    this.getData();
    this.allSwitch();

    // 横向滚动
    const wrap = document.getElementById('rin-week');
    const eventName = document.onmousewheel !== undefined ? 'mousewheel' : 'DOMMouseScroll';

    function mouse_wheel(ev) {
      const wrapWidth = wrap.scrollWidth / 25;

      const e = window.event || ev;
      if (e.detail > 0 || e.wheelDelta < 0) {
        wrap.scrollLeft += wrapWidth;
      } else {
        wrap.scrollLeft -= wrapWidth;
      }
    }

    function mac_mouse_wheel(e) {
      const wrapWidth = wrap.scrollWidth / 25;
      if (e.wheelDelta <= -120) {
        wrap.scrollLeft += wrapWidth;
      } else if (e.wheelDelta >= 120) {
        wrap.scrollLeft -= wrapWidth;
      }
    }
    const is = navigator.userAgent.toLowerCase().match(/mac |chrome/g);
    if (is && is.length > 1) {
      wrap.addEventListener(eventName, mac_mouse_wheel);
    } else {
      wrap.addEventListener(eventName, mouse_wheel);
    }

    // 键盘方向键滚动
    document.onkeydown = event => {
      const wrapWidth = wrap.scrollWidth / 80;
      const keycode = event.which || event.keyCode;

      // up:38 left:37 H:72 K:75
      if (keycode === 38 || keycode === 37 || keycode === 72 || keycode === 75) {
        // move right
        wrap.scrollLeft -= wrapWidth;
        return;
      }

      // down:40 right:39 L:76 J:74
      if (keycode === 40 || keycode === 39 || keycode === 76 || keycode === 74) {
        // move left
        wrap.scrollLeft += wrapWidth;
      }
    };
  },
};
</script>