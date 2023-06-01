<template>
  <div
    ref="sliderRef"
    class="slider-container"
    @mousedown="startDrag"
    @mousemove="drag"
    @mouseup="endDrag"
    @mouseleave="endDrag"
    @touchstart="startDrag"
    @touchmove="drag"
    @touchend="endDrag"
  >
    <canvas ref="canvasRef" class="slider-canvas"></canvas>
  </div>
</template>

<script>
export default {
  props: {
    imagesUrls: {
      type: Array,
      required: true,
    },
  },
  setup(props) {
    const images = ref([]);
    const canvasRef = ref(null);
    const sliderRef = ref(null);
    let dragging = false;
    let startX = 0;
    let currentX = ref(0);
    let totalWidth = ref(0); // Total width of all images combined

    const startDrag = (event) => {
      dragging = true;
      startX =
        (event.touches ? event.touches[0].clientX : event.clientX) -
        currentX.value;
      sliderRef.value.classList.add("drag-active");
    };

    const drag = (event) => {
      if (!dragging) return;
      const clientX = event.touches ? event.touches[0].clientX : event.clientX;
      const potentialX = clientX - startX;
      currentX.value = Math.min(
        0,
        Math.max(potentialX, sliderRef.value.clientWidth - totalWidth.value)
      );
    };

    const endDrag = () => {
      dragging = false;
      sliderRef.value.classList.remove("drag-active");
    };

    const loadImage = (url) => {
      return new Promise((resolve, reject) => {
        const img = new Image();
        img.onload = () => resolve(img);
        img.onerror = reject;
        img.src = url;
      });
    };

    const renderScene = () => {
      const ctx = canvasRef.value.getContext("2d");
      const width = sliderRef.value.clientWidth;
      const height = sliderRef.value.clientHeight;
      ctx.clearRect(0, 0, width * props.imagesUrls.length, height);
      images.value.forEach((img, index) => {
        let scale = Math.min(
          width / img.naturalWidth,
          height / img.naturalHeight
        );
        let imgWidth = img.naturalWidth * scale;
        let imgHeight = img.naturalHeight * scale;
        let imgX = index * width + (width - imgWidth) / 2 + currentX.value;
        let imgY = (height - imgHeight) / 2;
        ctx.drawImage(img, imgX, imgY, imgWidth, imgHeight);
      });
    };

    onMounted(async () => {
      for (const url of props.imagesUrls) {
        const img = await loadImage(url);
        images.value.push(img);
      }
      totalWidth.value = sliderRef.value.clientWidth * props.imagesUrls.length;
      canvasRef.value.width = totalWidth.value;
      canvasRef.value.height = sliderRef.value.clientHeight;
      renderScene();
    });

    watch(currentX, () => {
      requestAnimationFrame(renderScene);
    });

    return { canvasRef, sliderRef, startDrag, drag, endDrag };
  },
};
</script>

<style scoped>
.slider-container {
  width: 100%;
  height: 400px;
  overflow: hidden;
  cursor: grab;
  outline: 1px solid #ccc;
}

.slider-container.drag-active {
  cursor: grabbing;
}

.slider-canvas {
  height: 100%;
  background: rgb(242, 242, 242);
}
</style>
