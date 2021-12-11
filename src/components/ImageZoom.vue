<template>
  <div class="relative">
    <div class="lg:ml-10">
      <div
        class="relative product-image"
        @mouseenter="showZoomArea = true"
        @mouseleave="showZoomArea = false"
        @mousemove="zoomPreviewArea"
      >
        <div class="product-image-content">
          <img
            :src="imageList[showingImageIndex].url"
            class="center px-2 lg:px-0 product-image-current"
          />
        </div>
        <div
          v-if="showZoomArea"
          class="zoom-area hidden lg:block"
          :style="zoomArea"
        />
        <div
          v-if="true"
          class="previewer-area hidden lg:block"
          :style="previewerArea"
        >
          <div class="previewer">
            <img
              :src="imageList[showingImageIndex].url"
              :style="previewerImg"
            />
          </div>
          <div class="previewer-inner" />
        </div>
      </div>
    </div>
    <div class="list">
      <ul>
        <li v-for="(image, index) in imageList" :key="index">
          <img :src="image.url" :alt="image.alt" @click="onClick(index)" />
        </li>
      </ul>
    </div>
  </div>
</template>
<script lang="ts">
import { defineComponent, PropType, ref } from 'vue';

export default defineComponent({
  props: {
    imageList: {
      type: Array as PropType<{ key: number | string }[]>,
      default: (): [] => [],
    },
  },
  setup() {
    const showingImageIndex = ref(0);
    const showZoomArea = ref(false);

    const onClick = (index: number): void => {
      showingImageIndex.value = index;
    };

    const previewerImg = ref<{ [key: string]: string }>();
    const zoomArea = ref<{ [key: string]: string }>();
    const previewerArea = ref<{ [key: string]: string }>();
    const leftPosition = ref(0);
    const topPosition = ref(0);
    const sizeWidth = 250;
    const sizeHeight = 350;

    const zoomPreviewArea = (event: MouseEvent): void => {
      const productImageWrap = document.querySelector('.product-image');
      const productImageElement = document.querySelector(
        '.product-image-current'
      );
      const productImageElementWidth =
        productImageElement?.getBoundingClientRect().width ?? 0;
      const productImageElementHeight =
        productImageElement?.getBoundingClientRect().height ?? 0;
      const limitX = productImageElementWidth - sizeWidth;
      const limitY = productImageElementHeight - sizeHeight;

      previewerArea.value = {
        width: `${productImageElementWidth}px`,
        height: `${productImageElementHeight}px`,
        right: `${productImageElementWidth - 80 + 4}px`,
      };

      const rectObj = productImageWrap?.getBoundingClientRect();
      const scrollX = window.pageXOffset;
      const scrollY = window.pageYOffset;
      const offsetX = rectObj
        ? event.pageX - (rectObj.left + scrollX)
        : event.pageX;
      const offsetY = rectObj
        ? event.pageY - (rectObj.top + scrollY)
        : event.pageY;

      // ポインタの位置基準を中央にしている
      leftPosition.value = offsetX - sizeWidth / 2;
      topPosition.value = offsetY - sizeHeight / 2;
      if (leftPosition.value < 0) {
        leftPosition.value = 0;
      } else if (leftPosition.value > limitX) {
        leftPosition.value = limitX;
      }
      if (topPosition.value < 0) {
        topPosition.value = 0;
      } else if (topPosition.value > limitY) {
        topPosition.value = limitY;
      }
      const cutLeft = Math.floor((leftPosition.value / limitX) * 100);
      const cutTop = Math.floor((topPosition.value / limitY) * 100);
      zoomArea.value = {
        top: `${topPosition.value}px`,
        left: `${leftPosition.value}px`,
      };
      previewerImg.value = {
        'object-fit': 'none',
        'object-position': `${cutLeft}% ${cutTop}%`,
        width: `${productImageElementWidth / 2}px`,
        transform: `scale(2.0)`,
        'transform-origin': 'top left',
      };
    };
    return {
      showingImageIndex,
      onClick,
      zoomArea,
      showZoomArea,
      previewerImg,
      zoomPreviewArea,
      previewerArea,
    };
  },
});
</script>
<style lang="scss" scoped>
.product-image-content {
  width: 100%;
  @screen xl {
    width: 490px;
    height: 550px;
  }
}
table {
  @apply m-auto w-full;
}
img {
  &.center {
    width: 100%;
    @screen xl {
      width: 490px;
      height: auto;
    }
  }
}
.list {
  @apply w-full mt-2 mx-auto;
  ul {
    @apply overflow-x-auto whitespace-nowrap;
    li {
      @apply inline-block;
    }
  }
  img {
    width: 70px;
    height: 102px;
    @apply object-cover mx-1;
  }
  @screen lg {
    width: 490px;
    @apply mt-2 mx-auto;
    ul {
      @apply overflow-x-visible whitespace-pre-wrap px-2;
      li {
        @apply inline-block;
      }
    }
    img {
      width: 70px;
      height: 102px;
      @apply ml-0 mr-2 cursor-pointer;
    }
  }
}
.previewer-area {
  @apply absolute top-0 z-10;
}
.previewer-inner {
  @apply h-full;
}
.previewer {
  @apply absolute right-0 top-0 left-0 bottom-0;
}
.zoom-area {
  width: 250px;
  height: 350px;
  cursor: crosshair;
  @apply absolute bg-white opacity-50;
}
</style>
