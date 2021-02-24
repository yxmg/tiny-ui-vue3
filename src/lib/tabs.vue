<template>
  <div class="t-tabs">
    <div class="t-tabs-nav" ref="navWrapperRef">
      <div
        class="t-tabs-nav-item"
        :class="{ selected: keyProps[index] === activeKey}"
        v-for="(title, index) in titleProps"
        :ref="(el) => keyProps[index] === activeKey && (activeNav = el)"
        :key="index"
        @click="onTabClick(keyProps[index])"
      >
        <span>{{ title }}</span>
      </div>
      <div class="t-tabs-nav-indicator" ref="indicatorRef"></div>
    </div>
    <div class="t-tabs-content">
      <template v-if="forceRender">
        <component
          class="t-tabs-content-item"
          :is="selectedTab"
        />
      </template>
      <template v-else>
        <component
          class="t-tabs-content-item"
          v-for="slot in defaultSlots"
          v-show="slot.props.key === activeKey"
          :is="slot"
        />
      </template>
    </div>
  </div>
</template>

<script lang="ts">
import Tab from './tab.vue'
import {ref, computed, onMounted, watch, nextTick} from 'vue'

export default {
  name: "Tabs",
  props: {
    activeKey: [String, Number],
    forceRender: {
      type: Boolean,
      default: false
    }
  },
  setup(props, context) {
    // 检查子元素类型
    const defaultSlots = context.slots.default()
    if (defaultSlots.some(slot => slot.type !== Tab)) {
      throw new Error(`Tabs's children must be Tab`)
    }
    const titleProps = defaultSlots.map(slot => slot.props.title)
    const keyProps = defaultSlots.map(slot => slot.props.key)
    const selectedTab = computed(() => defaultSlots.find(slot => slot.props.key === props.activeKey))
    const onTabClick = (key) => {
      context.emit('update:activeKey', key)
    }
    const activeNav = ref<HTMLDivElement>(null)
    const navWrapperRef = ref<HTMLDivElement>(null)
    const indicatorRef = ref<HTMLDivElement>(null)
    // 设置indicator参数，宽度/偏移
    const updateIndicator = () => {
      const { width: activeNavWidth, left: activeNavLeft } = activeNav.value.getBoundingClientRect()
      const { left: navWrapperLeft } = navWrapperRef.value.getBoundingClientRect()
      indicatorRef.value.style.left = activeNavLeft - navWrapperLeft + 'px'
      indicatorRef.value.style.width = activeNavWidth + 'px'
    }
    onMounted(updateIndicator)
    watch(() => props.activeKey, () => {
      nextTick(updateIndicator)
    })

    // 获取选中Tab的宽度
    // 获取容器偏移-Tab偏移
    return {
      defaultSlots,
      titleProps,
      keyProps,
      selectedTab,
      onTabClick,
      activeNav,
      navWrapperRef,
      indicatorRef
    }
  }
}
</script>

<style lang="scss" scoped>
$color: #333;
$border-color: #d9d9d9;
$primary-color: #1890ff;

.t-tabs {
  &-nav {
    display: flex;
    color: $color;
    border-bottom: 1px solid $border-color;
    position: relative;

    &-indicator {
      position: absolute;
      left: 0;
      bottom: -1px;
      height: 3px;
      background-color: $primary-color;
      transition: width 0.25s, left 0.25s;
    }

    &-item {
      padding: 8px 0;
      margin: 0 16px;
      cursor: pointer;

      &:first-child {
        margin-left: 0;
      }

      &.selected {
        color: $primary-color;
      }
    }
  }

  &-content {
    padding: 8px 0;
  }
}
</style>