<template>
  <li v-on:mouseover="open" v-on:mouseout="setcurrentleaf(null)" class="nav-leaf">
    <!-- Handle top level items, may or may not be links -->
    <router-link v-if="childitems.navlink" :to="childitems.navlink" exact v-on:click="active = false" @focus.native="open">
      {{navtitle}}
    </router-link>
    <span v-if="!childitems.navlink" tabindex="0" v-on:focus="open">
      {{navtitle}}
    </span>
    <!-- Handle dropdowns -->
    <ul v-if="Object.keys(childitems).length > 1" class="drop-inner" v-bind:class="{ active: active }">
      <li
        v-for="(subnavlink, subnavtitle) in childitems"
        :key="subnavtitle"
        v-if="subnavtitle !== 'navlink'">
        <router-link :to="subnavlink" @mouseover.native="open" @focus.native="open">
          {{ subnavtitle }}
        </router-link>
      </li>
    </ul>
  </li>
</template>

<script>
export default {
  name: 'nav-drop',

  props: ['navtitle', 'currentleaf', 'setcurrentleaf', 'childitems'],

  computed: {
    active: function() {
      return this.currentleaf === this.navtitle
    }
  },

  methods: {
    open: function(event) {
      this.active = true;
      // Set parent component's state so all other dropdowns know if they should be active
      this.setcurrentleaf(this.navtitle);
    },
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/scss/vars';
@import '../assets/scss/mixins.scss';

a,
span {
  @include type-setting(0);
  color: $black;
  display: block;
  padding: $base-line-height / 2;
  &:hover, &.router-link-active{
    color: $red;
  }
}

.nav-leaf{
  position: relative;
  > .router-link-active {
    border-bottom: 0.125rem solid $red;
  }

}

.drop-inner{
  border-bottom: 2px solid $red;
  border-top: 0;
  position: absolute;
  background: white;
  left: 0;
  transition: .2s;
  opacity: 0;
  pointer-events: none;
  box-shadow: $box-shadow-inert;
  padding-bottom: $base-line-height / 2;
  padding-top: 0;
  &.active{
    opacity: 1;
    transition: .2s;
    left: 0;
    pointer-events: all;
    li{
      transition: .2s;
    }
  }
  li{
    display: block;
    transition: .2s;
    min-width: $base-line-height * 12;
    a{
      font-size: 1rem;
    }
  }
}

.main-nav-is-burger {
  .nav-leaf{
    border: none;
  }
  .drop-inner{
    border: none;
    position: relative;
    opacity: 1;
    padding: 0;
    background: none;
    left: 0;
    box-shadow: none;
    li{
      display: block;
      width: 100%;
      border-bottom: 1px solid lighten($red, 5%);
      position: relative;
      left: 0;
      &:after{
        content: '> ';
        color: white;
        left: $base-line-height;
        top: 25%;
        bottom: 0;
        position: absolute;
      }
    }
    li > a{
      color: white;
      padding-left: $base-line-height * 2;
      &:hover{
        color: white;
      }
    }
    li:first-child{
      border-top: 1px solid lighten($red, 5%);
    }
    &.active{
      left: 0;
    }
  }
  a, span {
    @include type-setting(0);
    padding: $base-line-height / 2 $base-line-height;
    color: white;
    display: block;
    &:hover{
      color: white;
    }
    &.router-link-active{
      color: white;
      font-weight: 900;
    }
  }
}
</style>
