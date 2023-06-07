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
    <canvas ref="canvasRef"></canvas>
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
    let dragging = ref(false);
    let startX = ref(0);
    let currentX = ref(0);

    const getClientX = (event) =>
      event.touches ? event.touches[0].clientX : event.clientX;
    const toggleDragging = (drag) => {
      dragging.value = drag;
      sliderRef.value.classList.toggle("drag-active", drag);
    };

    const startDrag = (event) => {
      toggleDragging(true);
      startX.value = getClientX(event);
    };

    const drag = (event) => {
      if (!dragging.value) return;
      const clientX = getClientX(event);
      const deltaX = clientX - startX.value;
      let newCurrentX = currentX.value + deltaX;
      const minCurrentX = -(
        sliderRef.value.clientWidth *
        (props.imagesUrls.length - 1)
      );
      if (newCurrentX > 0) newCurrentX = 0;
      if (newCurrentX < minCurrentX) newCurrentX = minCurrentX;
      currentX.value = newCurrentX;
      startX.value = clientX;
    };

    const endDrag = () => {
      toggleDragging(false);
    };

    const loadImage = async (url) => {
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
      ctx.clearRect(0, 0, width, height);
      let imgIndex = Math.min(
        Math.floor(Math.abs(currentX.value / width)),
        props.imagesUrls.length - 1
      );
      let nextImgIndex = Math.min(imgIndex + 1, props.imagesUrls.length - 1);
      let offsetX = currentX.value % width;
      let drawImage = (img, offset) => {
        let scale = Math.min(
          width / img.naturalWidth,
          height / img.naturalHeight
        );
        let imgWidth = img.naturalWidth * scale;
        let imgHeight = img.naturalHeight * scale;
        let imgX = (width - imgWidth) / 2 + offset;
        let imgY = (height - imgHeight) / 2;
        ctx.drawImage(img, imgX, imgY, imgWidth, imgHeight);
      };
      drawImage(images.value[imgIndex], offsetX);
      if (nextImgIndex !== imgIndex)
        drawImage(images.value[nextImgIndex], offsetX + width);
    };

    onMounted(async () => {
      for (const url of props.imagesUrls) {
        const img = await loadImage(url);
        images.value.push(img);
      }
      canvasRef.value.width = sliderRef.value.clientWidth;
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
  cursor: grab;
  outline: 1px solid #ccc;
}

.slider-container.drag-active {
  cursor: grabbing;
}
</style>
