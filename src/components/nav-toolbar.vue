<style scoped lang="less">
  @import "../less/colors.less";
  @team-avatar-size: 80px;
  #rin-toolbar {
    background-color: @color-primary-0;
    color: @color-primary-2;
    z-index: 1;
    .rin-button {
      margin: 8px;
      text-shadow: 0 0 5px @color-primary-3;
      z-index: 2;
    }
    .user-toolbar,
    .rin-logo {
      .rin-button {
        text-shadow: none;
        background: @color-primary-3;
        border: 4px solid @color-primary-2;
        i {
          z-index: 2;
          width: 40px;
          border-radius: 20px;
          background: @color-primary-3;
          transition: all .2s;
        }
        &:hover {
          background: @color-primary-4;
          border: 4px solid @color-primary-1;
          i {
            background: @color-primary-4;
          }
        }
        .rin-tooltip-wrap {
          z-index: 1;
        }
      }
    }
    .rin-logo {
      cursor: pointer;
      .img-wrap {
        width: 48px;
        height: 48px;
        margin-left: 4px;
        margin-right: 4px;
        img {
          border-radius: 50%;
          width: 48px;
          z-index: 2;
        }
      }
    }
    .rin-search,
    .rin-week,
    .rin-torrents,
    .rin-rss {
      position: absolute;
      margin: 4px 12px;
      i {
        font-size: 32px;
      }
    }
    .rin-search {
      bottom: 8px;
    }
    .rin-week {
      bottom: 56px;
    }
    .rin-rss {
      bottom: 104px;
    }
    .rin-torrents {
      bottom: 152px;
    }
    .team {
      text-align: center;
      float: left;
      z-index: 10;
      .team-icon {
        display: block;
        margin: 0;
        width: 40px;
        height: 40px;
        border-radius: 20px;
        font-size: @team-avatar-size * 0.7;
        line-height: @team-avatar-size;
        background-size: cover;
      }
    }
    .teams {
      margin-top: -14px;
      margin-left: -10px;
      width: 158px;
      height: 48px;
      .team .team-icon {
        margin: 4px;
        background-color: white;
        &:last-child {
          margin-right: 0;
        }
      }
    }
  }
</style>

<template>
<div id="rin-toolbar" class="rin-main-bar rin-col">

  <div class="rin-logo">
    <div class="rin-button round img-wrap" @click="userSignAction($event)">
      <img src="../assets/akarin.jpg" v-if="!user._id" />
      <img :src="'https://bangumi.moe/avatar/'+ user.emailHash" v-if="user._id" />
      <tooltip :info="$t('Login')" v-if="!user._id"></tooltip>
    </div>

    <info-box :user="user" arrow="right" v-if="user._id"></info-box>
  </div>

  <div class="user-toolbar" v-if="user._id">
    <a class="rin-button round" v-if="user.info && user.info.teams.length > 0">
      <span class="team">
        <span class="team-icon" :style="{'background-image': 'url(' + getIcon(user.info.teams[0]) +')'}"></span>
      </span>
      <tooltip>
        <div class="teams" :style="{width: (48 * (user.info.teams.length - 1) - 4) + 'px' }" slot="html" v-if="user.info">
            <span class="team" v-for="i in user.info.teams.slice(1)">
                <span class="team-icon" :style="{'background-image': 'url(' + getIcon(i) +')'}"></span>
            </span>
        </div>
      </tooltip>
    </a>
    <router-link class="rin-button round" to="/torrent/upload">
      <!--<i class="material-icons">&#xE89D;</i>-->
      <i class="fa fa-plus"></i>
      <tooltip :info="$t('Publish')"></tooltip>
    </router-link>
    <a class="rin-button round">
      <i class="fa fa-rss"></i>
      <tooltip :info="$t('Customize RSS')"></tooltip>
    </a>
    <a class="rin-button round" @click="userSignout">
      <i class="fa fa-sign-out"></i>
      <tooltip :info="$t('Logout')"></tooltip>
    </a>
  </div>

  <router-link class="rin-button rin-torrents" v-show="!is_homepage" :to="{ path: '/', exact: true }">
    <i class="fa fa-arrow-left"></i>
    <tooltip :info="$t('Back to Index')"></tooltip>
  </router-link>

  <router-link class="rin-button rin-week" to="/bangumi/list">
    <!--<i class="material-icons">&#xE8EF;</i>-->
    <i class="fa fa-list"></i>
    <tooltip :info="$t('Bangumi List')"></tooltip>
  </router-link>

  <a class="rin-button rin-rss" href="https://bangumi.moe/rss/latest" target="_blank">
    <!--<i class="material-icons" style="transform: rotate(45deg);padding-top: 1px;padding-left: 1px;">&#xE63E;</i>-->
    <i class="fa fa-rss"></i>
    <tooltip :info="$t('RSS')"></tooltip>
  </a>

  <span class="rin-button rin-search" id="rin-search"
    @mouseenter="searchBarShow"
    @mouseleave="searchBarHide"
    @click="searchBarToggle">
    <!--<i class="material-icons">&#xE8B6;</i>-->
    <i class="fa fa-search"></i>
    <tooltip :info="$t('Search')"></tooltip>
  </span>

  <!-- Search Added By LancerComet at 23:07, 2015.12.08. -->
  <search-bar v-bind:class="{'show': searchBar.visible, 'fixed': searchBar.fixed, 'hide': !searchBar.visible && !searchBar.fixed}"></search-bar>

</div>
</template>

<script>
  import NavToolbarSearch from './nav-toolbar-search';
  import NavToolbarInfobox from './nav-toolbar-infobox';
  import NavTooltip from './nav-tooltip';

  export default {
    name: 'NavToolbar',
    data() {
      return {
        user: {},
        searchBar: {
          visible: false,
          fixed: false,
        },
        signin_form_opened: false,
        is_homepage: (location.pathname === '/'),
      };
    },
    methods: {
      // Definition: 搜索栏显示事件.
      searchBarShow() {
        this.searchBar.visible = true;
        this.$emit('recentProgramRequest'); // 广播至 search-bar.
      },
      // Definition: 搜索栏隐藏事件.
      searchBarHide() {
        this.searchBar.visible = false;
      },
      // 判断el是否在selector内
      closest(el, selector) {
        let matchesFn;

        // find vendor prefix
        ['matches', 'webkitMatchesSelector', 'mozMatchesSelector', 'msMatchesSelector', 'oMatchesSelector'].some((fn) => {
          if (typeof document.body[fn] === 'function') {
            matchesFn = fn;
            return true;
          }
          return false;
        });

        // traverse parents
        let ele = el;
        while (ele !== null) {
          const parent = ele.parentElement;
          if (parent !== null && parent[matchesFn](selector)) {
            return parent;
          }
          ele = parent;
        }

        return null;
      },
      // Definition: 搜索图标点击 Toggle 事件.
      searchBarToggle() {
        this.searchBar.fixed = !this.searchBar.fixed;

        /* *** 点击其他地方时，关闭搜索 *** */
        if (this.searchBar.fixed) {
          const el = document.querySelectorAll('.rin-search-bar')[0];
          const openSearchBtn = document.getElementById('rin-search'); // 打开搜索的按钮
          const openSearchIcon = openSearchBtn.children[0]; // 按钮内的icon

          function search(ev) {
            if (el !== ev.target && !ev.target.closest('.rin-search-bar') && ev.target !== openSearchIcon) {
              this.searchBar.fixed = false;
              search.bind(this);
              document.removeEventListener('click', search, false);
            }
          }
          search.bind(this);
          document.addEventListener('click', search, false);
        }
      },

      // sigin or jump to user info page
      userSignAction() {
        if (!this.user._id) {
          this.signin_form_opened = true;
          this.$dispatch('displaySigninForm');
        } else {
          this.$router.go(`/user/${this.user._id}`);
        }
      },

      userSignout() {
        if (this.user._id) {
          this.$dispatch('UserSignOut');
        }
      },


      getUserInfo() {
        // console.log(this.user)
        this.$http.get(`https://bangumi.moe/api/v2/user/${this.user._id}`).then((response) => {
          this.$set('user.info', response.data);
        });
      },

      getIcon(i) {
        return i.icon ? `https://bangumi-moe.phoenixstatic.com/${i.icon}` : require('../assets/akarin.jpg');
      },

    },
    components: {
      'search-bar': NavToolbarSearch,
      'info-box': NavToolbarInfobox,
      tooltip: NavTooltip,
    },
    events: {
      UserSignInOK(user) {
        this.user = user;
        this.getUserInfo();
        if (this.signin_form_opened) {
          this.signin_form_opened = false;
          this.$dispatch('hideSigninForm');
        }
      },
      UserSignInFailed() {
        this.user = {};
      },
      UserSignOutOK() {
        this.user = {};
      },
    },
    watch: {
      '$route' () {
        this.is_homepage = (location.pathname === '/');
      },
    },
  };
</script>
