<template>
  <template v-if="visible">
    <div class="t-dialog-overlay"></div>
    <div class="t-dialog-wrapper">
      <div class="t-dialog">
        <div class="t-dialog-header">
          <span class="t-dialog-title">标题</span>
          <span class="t-dialog-close"></span>
        </div>
        <div class="t-dialog-content">内容</div>
        <div class="t-dialog-footer">
          <Button theme="primary">确认</Button>
          <Button>取消</Button>
        </div>
      </div>
    </div>
  </template>
</template>

<script lang="ts">
import Button from './button.vue'

export default {
  name: "dialog",
  props: {
    visible: {
      type: Boolean,
      default: false
    }
  },
  components: { Button }
}
</script>

<style lang="scss" scoped>
$radius: 4px;
$border-color: #d9d9d9;
.t-dialog {
  background: white;
  border-radius: $radius;
  box-shadow: 0 0 3px fade_out(black, 0.5);
  min-width: 15em;
  max-width: 90%;

  &-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: fade_out(black, 0.5);
    z-index: 10;
  }

  &-wrapper {
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    z-index: 11;
  }

  &-header {
    padding: 12px 16px;
    border-bottom: 1px solid $border-color;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 20px;
  }

  &-content {
    padding: 12px 16px;
  }

  &-footer {
    border-top: 1px solid $border-color;
    padding: 12px 16px;
    text-align: right;

    .t-button ~ .t-button {
      margin-left: 10px;
    }
  }

  &-close {
    position: relative;
    display: inline-block;
    width: 16px;
    height: 16px;
    cursor: pointer;

    &::before,
    &::after {
      content: '';
      position: absolute;
      height: 1px;
      background: black;
      width: 100%;
      top: 50%;
      left: 50%;
    }

    &::before {
      transform: translate(-50%, -50%) rotate(-45deg);
    }

    &::after {
      transform: translate(-50%, -50%) rotate(45deg);
    }
  }
}
</style>