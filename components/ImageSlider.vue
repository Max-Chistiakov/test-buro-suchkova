<template>
    <transition
      appear
    >
      <div
        class="relative overflow-hidden bg-cover w-screen h-full bg-center bg-no-repeat flex justify-center items-center"
        :style="{ 'background-image': `url(${mainImage})` }"
      >
        <div
          v-for="image in normalizedImages"
          :key="image"
          class="slider-image w-full h-full min-h-80 min-w-80 absolute top-0 left-0 overflow-hidden will-change-transform"
        >
          <div
            class="bg-cover w-screen h-full bg-center bg-no-repeat"
            :style="{ 'background-image': `url(${image})` }"
          >
          </div>
        </div>

<!--   условный рендеринг для предотвращения отображения, если signImage не передан       -->
        <div
            v-if="signImage"
            class="flex w-full h-full absolute top-0 left-0 px-5 pb-[10%] items-end justify-center overflow-hidden">
<!--    ref используется вместо селектора по классу, чтобы напрямую ссылаться на DOM элемент       -->
          <div
              ref="refSignImage"
              class="bg-contain bg-center bg-no-repeat w-full h-full max-w-[1400px] max-h-[200px] will-change-transform"
              :style="{ 'background-image': `url(${signImage})` }"
          ></div>
        </div>
      </div>
    </transition>
</template>

<script>
import {gsap} from 'gsap'

export default {
  props: {
    images: {
      type: Array,
      required: true
    },
    mainImage: {
      type: String,
      required: true
    },
    signImage: {
      type: String,
      default: ''
    },
    duration: {
      type: Number,
      default: 1.1
    },
    delayEachImage: {
      type: Number,
      default: 0.2
    }
  },
  setup(props) {
    // что можно сделать ещё для улучшения производительности, и чего я не делал в связи с решением не тратить слишком много времени на тестовое,
    // и в принципе с первым опытом с gsap и vueuse\motion:
    // можно углубиться в доку gsap и почитать что они предлагают в плане оптимизации анимаций,
    // можно вынести CSS стили переиспользуемых анимаций отдельно, например transition: transform 1s ease-in-out, opacity 1s ease-in-out
    // можно использовать рефы и для основного массива изображений, но я бы сначала протестировал производительность (чего я не делал)
    // можно использовать ленивую загрузку, но это тоже надо предварительно попробовать
    // можно кэшировать изображения, или с помощью Service Workers, или библиотек для Vue, или использовать кэш самостоятельно (имею ввиду cache.put, cache.open )

    // анимация не один-в-один, прошу прощения, её можно привести в эталонный вид,
    // думаю можно вручную задавать кривую Безье или использовать keyframes, но снова - я не углублялся в доку gsap в рамках тестового
    // можно дальше добавлять сущности в пропсы, например передавать тип анимации ease

    // в любом случае: тестовое интересное и не сильно напряжное, при любом решении буду рад фидбэку по коду, можно писать напрямую в tg: @Max_Chistiakov

    // добавляем фоновую картинку, чтобы она приезжала последней. не очень хорошо копировать массив,
    // но в рамках тестового и надежды на разумное количетсво изображений...
    const normalizedImages = computed(() => {
      return [...props.images, props.mainImage ]
    })
    const signShowDelay = ref((props.images.length) * props.delayEachImage)
    const refSignImage = ref(null)

    function enter() {
      gsap.fromTo(
        '.slider-image',
        { x: '-100%', opacity: 1 },
        {
          x: '0%',
          opacity: 1,
          duration: props.duration,
          stagger: {
            each: props.delayEachImage,
          },
          ease: 'sine.inOut',
        }
      )
    }

    function animateSign() {
      gsap.fromTo(
        refSignImage.value,
        { x: '-100%', y: '0%', scale: 1, opacity: 0 },
        {
          x: '0',
          y: '0',
          scale: 1,
          opacity: 1,
          duration: props.duration,
          delay: signShowDelay.value,
          ease: 'sine.inOut',
        }
      )
    }

    onMounted(() => {
      enter()
      animateSign()
    })

    return {
      normalizedImages,
      refSignImage
    }
  }
}
</script>
