<template>
  <main id="main">
    <img v-if="imgRef" :src="imgRef" alt="Картинка" />

    <h4 >{{ imageError }}</h4>
    <div
      class="drag__wrapper"
      :style="styleComputed"
      @dragenter="setIsDragging"
      @dragexit="removeDragging"
      @drop.prevent.stop.capture="(e) => handleDrop(e)"
      @dragover.capture.prevent.stop
      @drag.capture.prevent.stop
    >
      <div class="drag__box">
        <p class="drag__text">{{ textComputed }}</p>
      </div>
    </div>
  </main>
</template>

<script setup>
import { computed, ref } from "vue";

const isDraggingStyle = {
  backgroundColor: "black",
  border: "3px dashed white",
  color: "white",
};

const isNotDragginStyle = {
  backgroundColor: "white",
  border: "3px solid transparent",
  color: "black",
};

const isNotDraggingText = "Перетащите изображения сюда";
const isDraggingText = "Отпустите изображения";

const isDragging = ref(false);
const imgRef = ref(null);
const imageError = ref(null);

const styleComputed = computed(() => {
  if (isDragging.value) return isDraggingStyle;
  else return isNotDragginStyle;
});

const textComputed = computed(() => {
  if (isDragging.value) return isDraggingText;
  else return isNotDraggingText;
});

function setIsDragging() {
  isDragging.value = true;
}

function removeDragging() {
  isDragging.value = false;
}

function handleDrop(ev) {
  removeDragging();
  const img = ev?.dataTransfer?.files[0];
  const isImage = checkIfImage(img);

  if (isImage) {
    const fileReader = new FileReader();

    const onLoad = (ev) => {
      const { result } = ev.target;
      imgRef.value = result;
    };

    fileReader.onload = onLoad;
    fileReader.readAsDataURL(img);
  } else {
    imageError.value = "Ошибка загрузки изображения";
  }
}

function checkIfImage(img) {
  const acceptedImageTypes = ["image/gif", "image/jpeg", "image/png"];

  const { type } = img;

  return acceptedImageTypes.includes(type);
}
</script>

<style lang="scss" scoped>
main {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  gap: 4rem;
  justify-content: center;
  align-items: center;

  img {
    aspect-ratio: 1/1;
    height: 40vh;
    object-fit: cover;
  }

  h4 {
    font-size: 4rem;
    font-weight: 700;
    color: red;
  }
}

.drag {
  &__box {
    width: 60rem;
    height: 30rem;

    border: 2px solid;

    display: grid;
    place-items: center;

    font-size: 3rem;

    transition: 0.1s all ease;

    p {
      transition: inherit;
    }
  }
}
</style>
