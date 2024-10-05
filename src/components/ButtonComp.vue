<template>
  <button class="order__button" @:click="addToCart">
    <img class="order__image" :src="icon" />
  </button>
</template>

<script lang="ts" setup>
import { defineProps, ref, computed, onMounted } from 'vue'

interface CartItem {
  code: string | null;
}

const props = defineProps<{
  firstSrc: string;
  secondSrc: string;
  type: string;
  object: CartItem;
}>()

const isInCart = ref<boolean>(false)

const icon = computed(() => {
  if (isInCart.value === true) {
    return props.secondSrc
  } else {
    return props.firstSrc
  }
})

onMounted(() => {
  const key = props.type + '-' + props.object.code
  isInCart.value = (localStorage.getItem(key) !== null)
})

const addToCart = () => {
  const key = props.type + '-' + props.object.code
  if (isInCart.value) {
    localStorage.removeItem(key)
    isInCart.value = false
  } else {
    localStorage.setItem(key, JSON.stringify(props.object))
    isInCart.value = true
  }
}
</script>
