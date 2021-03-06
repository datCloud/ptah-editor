<script>
import { mapState, mapActions } from 'vuex'
import * as _ from 'lodash-es'

export default {
  name: 'ControlBox',

  data () {
    return {
      lockMargins: false,
      lockPaddings: false,
      padding: {
        paddingTop: 'padding-top',
        paddingRight: 'padding-right',
        paddingBottom: 'padding-bottom',
        paddingLeft: 'padding-left'
      },
      margin: {
        marginTop: 'margin-top',
        marginRight: 'margin-right',
        marginBottom: 'margin-bottom',
        marginLeft: 'margin-left'
      }
    }
  },

  props: {
    hideMargin: {
      type: Boolean,
      default: false
    },
    hidePadding: {
      type: Boolean,
      default: false
    }
  },

  computed: {
    ...mapState('Sidebar', [
      'settingObjectOptions',
      'settingObjectElement',
      'device',
      'isMobile'
    ]),

    marginLeft: {
      get () {
        return this.getStyleNumberValue('margin-left')
      },

      set (value) {
        this.setter('margin', 'marginLeft', value)
      }
    },

    marginRight: {
      get () {
        return this.getStyleNumberValue('margin-right')
      },

      set (value) {
        this.setter('margin', 'marginRight', value)
      }
    },

    marginTop: {
      get () {
        return this.getStyleNumberValue('margin-top')
      },

      set (value) {
        this.setter('margin', 'marginTop', value)
      }
    },

    marginBottom: {
      get () {
        return this.getStyleNumberValue('margin-bottom')
      },

      set (value) {
        this.setter('margin', 'marginBottom', value)
      }
    },

    paddingLeft: {
      get () {
        return this.getStyleNumberValue('padding-left')
      },

      set (value) {
        this.setter('padding', 'paddingLeft', value)
      }
    },

    paddingRight: {
      get () {
        return this.getStyleNumberValue('padding-right')
      },

      set (value) {
        this.setter('padding', 'paddingRight', value)
      }
    },

    paddingTop: {
      get () {
        return this.getStyleNumberValue('padding-top')
      },

      set (value) {
        this.setter('padding', 'paddingTop', value)
      }
    },

    paddingBottom: {
      get () {
        return this.getStyleNumberValue('padding-bottom')
      },

      set (value) {
        this.setter('padding', 'paddingBottom', value)
      }
    },

    isMain () {
      return this.settingObjectElement.classList.contains('ptah-g-main')
    },

    isChild () {
      return this.settingObjectElement.classList.contains('ptah-g-child')
    },

    isTextTooltipMain () {
      return this.isMain ? this.$t('s.sectionOfGroup') : ''
    },

    tooltipMain () {
      return this.isMain ? 'tooltip' : ''
    },

    isTextTooltipChild () {
      return this.isChild ? this.$t('s.sectionOfGroup') : ''
    },

    tooltipChild () {
      return this.isChild ? 'tooltip' : ''
    }
  },

  methods: {
    ...mapActions('Sidebar', [
      'updateSettingOptions'
    ]),

    getStyleNumberValue (prop) {
      let props = {}
      let s = {}
      let styles = this.settingObjectOptions.styles
      let stylesMedia = {}

      if (this.settingObjectOptions.media && this.settingObjectOptions.media['is-mobile']) {
        stylesMedia = this.settingObjectOptions.media['is-mobile']
      } else {
        stylesMedia[prop] = styles[prop]
      }

      if (this.isMobile) {
        props = stylesMedia
      } else {
        props = styles
      }

      s = props[prop]

      if (s === undefined || this.isMain || this.isChild) {
        // get values from node
        let style = window.getComputedStyle(this.settingObjectElement)
        s = style[_.camelCase(prop)]
      }
      return parseInt(s)
    },

    setter (group, prop, value) {
      if ((group === 'padding' && this.lockPaddings) || (group === 'margin' && this.lockMargins)) {
        Object.keys(this[group]).forEach((key) => this.update(group, key, value))
      } else {
        this.update(group, prop, value)
      }
    },

    update (group, prop, value) {
      let [props, styles, media, property] = [{}, {}, {}, this[group][prop]]
      if (value === '') value = 0

      styles[property] = value + 'px'
      media['is-mobile'] = {}
      media['is-mobile'][property] = value + 'px'

      this.isMobile ? props = { 'media': media } : props = { 'styles': styles }

      this.updateSettingOptions(_.merge({}, this.settingObjectOptions, props))
    }
  }
}
</script>

<template>
<div>
  <base-label>
    {{ $t('c.editIndents') }}
  </base-label>
  <div class="control-box">

    <!-- preview -->
    <div class="control-box__element-edge">
      <div class="control-box__content-edge">
      </div>
    </div>
    <div class="control-box__title-m" v-if="!hideMargin">{{ $t('c.margin') }}</div>
    <div class="control-box__title-p" v-if="!hidePadding">{{ $t('c.padding') }}</div>
    <!-- CONTROLS -->
    <!-- margin -->
    <template v-if="!hideMargin">
      <base-number-field v-model="marginLeft" class="ctrl ctrl__m-left" pattern=""></base-number-field>
      <base-number-field v-model="marginRight" class="ctrl ctrl__m-right"></base-number-field>
      <base-number-field v-model="marginTop" class="ctrl ctrl__m-top"></base-number-field>
      <base-number-field v-model="marginBottom" class="ctrl ctrl__m-bottom"
        :disabled="isChild"
        :[tooltipChild]="isTextTooltipChild"
        tooltip-position="bottom"
        >
      </base-number-field>
    </template>
    <!-- padding -->
    <template v-if="!hidePadding">
      <base-number-field v-model="paddingLeft" class="ctrl ctrl__p-left"></base-number-field>
      <base-number-field v-model="paddingRight" class="ctrl ctrl__p-right"></base-number-field>
      <base-number-field v-model="paddingTop" class="ctrl ctrl__p-top"></base-number-field>
      <base-number-field v-model="paddingBottom" class="ctrl ctrl__p-bottom"
        :disabled="isMain"
        :[tooltipMain]="isTextTooltipMain"
        tooltip-position="bottom"
        ></base-number-field>
    </template>
    <!-- locks -->
    <a href="#"
       class="control-box__lock control-box__lock--margin"
       :class="{ 'active': lockMargins }"
       @click.prevent="lockMargins = !lockMargins"
       v-if="!hideMargin"
      >
      <icon-base name="lock"></icon-base>
    </a>
    <a href="#"
       class="control-box__lock control-box__lock--padding"
       :class="{ 'active': lockPaddings }"
       @click.prevent="lockPaddings = !lockPaddings"
       v-if="!hidePadding"
      >
      <icon-base name="lock"></icon-base>
    </a>
  </div>
</div>
</template>

<style lang="sass" scoped>
@import '../../../assets/sass/_colors.sass'
@import '../../../assets/sass/_variables.sass'

.control-box
  $this: &

  width: 100%
  max-width: 24rem
  height: 21rem

  box-sizing: border-box
  border: 2px dotted #999999

  position: relative

  display: flex
  justify-content: center
  align-items: center

  font-size: 1.4rem
  transition: border-color 0.1s ease
  &:hover
    border-color: $dark-blue-krayola
    #{$this}__element-edge
      border-color: $dark-blue-krayola

  &__element-edge
    width: 153px
    height: 126px
    border: 2px solid #6D6D6D

    display: flex
    justify-content: center
    align-items: center
    transition: border-color 0.1s ease

  &__content-edge
    width: 68px
    height: 38px
    border: 2px dotted #999999

  &__title-m
    position: absolute
    top: 4px
    left: 5px

  &__title-p
    position: absolute
    top: 45px
    left: 45px

  &__lock
    position: absolute
    color: #C6C6C6
    text-decoration: none
    transition: color 0.1s ease
    &.active
      color: $dark-blue-krayola

    &:hover
      color: rgba($dark-blue-krayola, 0.8)

    &--margin
      right: 8px
      bottom: 8px

    &--padding
      right: 49px
      bottom: 52px

.ctrl
  position: absolute
  width: 2.6rem

  &__m-left
    left: 7px
    top: calc(50% - 13px)

  &__m-right
    right: 7px
    top: calc(50% - 13px)

  &__m-top
    right: calc(50% - 13px)
    top: 4px

  &__m-bottom
    right: calc(50% - 13px)
    bottom: 4px

  &__p-left
    top: calc(50% - 13px)
    left: 49px

  &__p-right
    top: calc(50% - 13px)
    right: 47px

  &__p-top
    right: calc(50% - 13px)
    top: 51px

  &__p-bottom
    right: calc(50% - 13px)
    bottom: 52px
</style>
