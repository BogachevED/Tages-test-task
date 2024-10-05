<template>
  <header class="header">
    <nav aria-label="header__navigator-list">
      <ul class="header__navigator-list font-SF-UI-Text--light">
        <li class="header__navigator-item">
          <a class="header__navigator-link" @click="swithTitle('Главная', 1)">Главная</a>
        </li>
        <li class="header__navigator-separator"> / </li>
        <li class="header__navigator-item">
          <a class="header__navigator-link" @click="swithTitle('Системы хранения', 2)">Системы хранения</a>
        </li>
        <li class="header__navigator-separator"> / </li>
        <li class="header__navigator-item">
          <a class="header__navigator-link" @click="swithTitle('Комплекты стеллажных систем', 3)">Комплекты стеллажных систем</a>
        </li>
      </ul>
    </nav>
  </header>
  <main class="main">
    <section class="title">
      <p class="title__text font-SF-Pro-Display--semibold">{{ title }}</p>
    </section>
    <section v-if="pseudoState === 3" class="сontent">
      <section class="selectors">
        <div class="selectors__body">
          <p class="selectors__label font-SF-Pro-Display--light">Сортировать по:</p>
          <select class="selectors__selector font-SF-Pro-Display--light" v-model="sortPrice">
            <option class="selectors__option" value="none">Отсутсвует</option>
            <option class="selectors__option" value="ascending">Цена по возрастанию</option>
            <option class="selectors__option" value="descending">Цена по убыванию</option>
          </select>
        </div>
        <div class="selectors__body">
          <p class="selectors__label font-SF-Pro-Display--light">Материал:</p>
          <select class="selectors__selector font-SF-Pro-Display--light" v-model="sortMaterial">
            <option class="selectors__option" value="0">Не указан</option>
            <option v-for="(material, index) in materials" :key="index" class="selectors__option" :value="material.id">{{ material.name }}</option>
          </select>
        </div>
      </section>
      <section class="products-table">
        <div class="table">
          <div v-for="(item, index) in sortedItems" :key="index" class="table__card product-card">
            <div class="product-card__discount">
              <p v-if="item.price.old_price !== null" class="product-card__discount-text font-SF-Pro-Display--light">Скидка</p>
            </div>
            <div class="product-card__image-field">
              <img class="product-card__image" :src="item.image.url"/>
            </div>
            <div class="product-card__info-field">
              <p class="product-card__code font-SF-UI-Text--light">{{ item.code }}</p>
              <p class="product-card__name font-SF-UI-Text--semibold">{{ item.name }}</p>
              <div class="product-card__order order">
                <div class="order__prices">
                  <p class="order__price font-SF-UI-Text--light crossed" v-if="item.price.old_price !== null">{{ item.price.old_price + '₽'}}</p>
                  <p class="order__price font-SF-UI-Text--semibold">{{ item.price.current_price + '₽'}}</p>
                </div>
                <div class="order__buttons">
                  <ButtonComp :firstSrc="'./img/icons/purchase.svg'" :secondSrc="'./img/icons/chousen.svg'" :type="'cart'" :object="item"/>
                  <ButtonComp :firstSrc="'./img/icons/favourites-uncheck.svg'" :secondSrc="'./img/icons/favourites-check.svg'" :type="'favourites'" :object="item"/>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
    </section>
  </main>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue'
import ButtonComp from './ButtonComp.vue'
import JSONitems from '../files/items.json'
import Materials from '../files/materials.json'

interface Item {
  id: string;
  name: string;
  code: string | null;
  price: {
    'old_price': number | null;
    'current_price': number;
  };
  image: {
    url: string;
  }
  material: number;
}

interface Material {
  id: string;
  name: string;
}

const items = ref<Item[]>(JSONitems)
const materials = ref<Material[]>(Materials)

const title = ref<string>('Комплекты стеллажных систем')
const pseudoState = ref<number>(3)

const swithTitle = (titleVal: string, statVal: number): void => {
  title.value = titleVal
  pseudoState.value = statVal
}

const sortPrice = ref<string>('none')
const sortMaterial = ref<string>('0')

const sortedItems = computed<Item[]>(() => {
  let newArr: Item[] = [...items.value]
  if (sortPrice.value === 'none') {
    newArr = newArr.sort((a: Item, b: Item) => parseInt(a.id) - parseInt(b.id))
  } else if (sortPrice.value === 'ascending') {
    newArr = newArr.sort((a: Item, b: Item) => a.price.current_price - b.price.current_price)
  } else if (sortPrice.value === 'descending') {
    newArr = newArr.sort((a: Item, b: Item) => b.price.current_price - a.price.current_price)
  }
  if (sortMaterial.value !== '0') {
    const materialId = parseInt(sortMaterial.value, 10)
    newArr = newArr.filter((item: Item) => item.material === materialId)
  }
  return newArr
})
</script>
