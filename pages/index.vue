<template>
  <div class="relative w-[100vw] h-[100vh] overflow-hidden flex justify-center">
<!--  сейчас используются дефолтные значения времени и задержки анимации, но можно использовать пропсы
    :duration="3"
    :delay-each-image="0.5"  -->
    <ImageSlider
      :images="images"
      :main-image="img_0"
      :sign-image="sign"
    />
  </div>
</template>

<script>
import img_0 from "assets/slider/image_0.png";
import sign from "assets/slider/sign.png";

export default {
  setup() {
    // Тут может быть запрос с сервера
    const images = [
      'https://api.forpeople.su/uploads/1_f07d58a37f.webp',
      'https://api.forpeople.su/uploads/2_c96c3566a5.webp',
      'https://api.forpeople.su/uploads/3_028c75c79e.webp',
      'https://api.forpeople.su/uploads/4_5cb0874fa4.webp',
      'https://api.forpeople.su/uploads/5_7f2e5fc9c5.webp',
      'https://api.forpeople.su/uploads/66_8bbc0e995c.webp'
    ]

    return {
      images,
      img_0,
      sign
    }
  }
}
</script>
