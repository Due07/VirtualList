<template>
  <div class="recycle-scroller-container" @scroll="setPool" ref="container">
    <div class="recycle-scroller-wrapper" :style="{
        height: `${totalSize}px`,
      }">
        <!-- paddingTop: `${paddingTop}px`, -->
      <div
        class="recycle-scroller-item"
        v-for="poolItem in pool"
        :key="poolItem.item[keyField]"
        :style="{
          transform: `translateY(${poolItem.position}px)`,
        }"
      >
        <slot :item="poolItem.item"></slot>
      </div>
    </div>
  </div>
</template>

<script>
var prev = 2,
  next = 2;
export default {
  props: {
    // 数据的数组
    items: {
      type: Array,
      default: () => [],
    },
    // 每条数据的高度
    itemSize: {
      type: Number,
      default: 0,
    },
    keyField: {
      // 给我的items数组中，每个对象哪个属性代表唯一且稳定的编号
      type: String,
      default: 'id',
    },
  },
  data() {
    return {
      // { item: 原始数据, position: 该数据对应的偏移位置 }
      pool: [], // 渲染池，保存当前需要渲染的数据
      paddingTop: 0
    };
  },
  mounted() {
    this.setPool();
    window.vm = this;
  },
  computed: {
    totalSize() {
      return this.items.length * this.itemSize; // 总高度
    },
  },
  methods: {
    setPool() {
      // 滚动高度
      const scrollTop = this.$refs.container.scrollTop;
      // 可视区高度
      const height = this.$refs.container.clientHeight;
      console.log(scrollTop, height);
      // 滚动高度 / 内容高度 => 第几个
      let startIndex = Math.floor(scrollTop / this.itemSize);
      // 滚动高度 + 可视区高度 / 内容高度 => 第几个后不在视图了
      let endIndex = Math.ceil((scrollTop + height) / this.itemSize);
      console.log(startIndex, endIndex);

      startIndex -= prev;
      if (startIndex < 0) {
        startIndex = 0;
      }
      endIndex += next;
      // 移动高度
      const startPos = startIndex * this.itemSize;
      // this.paddingTop = startIndex * this.itemSize;
      this.pool = this.items.slice(startIndex, endIndex).map((it, i) => ({
        item: it,
        position: startPos + i * this.itemSize,
      }));
    },
  },
};
</script>

<style>
.recycle-scroller-container {
  overflow: auto;
}
.recycle-scroller-wrapper {
  box-sizing: border-box;
  position: relative;
}
.recycle-scroller-item {
  position: absolute;
  width: 100%;
  left: 0;
  top: 0;
}
</style>
