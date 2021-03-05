<template>
  <div id="menus">
    <h1>Default</h1>
    <menu-picker :menus="menus" :menu-id="'menu1'" />
    <h1>Disable</h1>
    <menu-picker :menus="menus" :menu-id="'menu2'" disable-child />
  </div>
</template>

<script>
// @ is an alias to /src
import MenuPicker from "@/components/MenuPicker.vue";

export default {
  name: "Home",
  components: {
    MenuPicker
  },
  data: () => ({
    menus: [
      {
        id: 9,
        name: "콜라",
        parentId: 2,
        price: 2000,
        salePrice: null,
        order: 0
      },
      {
        id: 17,
        name: "팔보채",
        parentId: 4,
        price: 25000,
        salePrice: null,
        order: 0
      },
      {
        id: 13,
        name: "짬뽕",
        parentId: 3,
        price: 8000,
        salePrice: null,
        order: 0
      },
      {
        id: 6,
        name: "연태고량주",
        parentId: 1,
        price: 25000,
        salePrice: 20000,
        order: 6
      },
      {
        id: 7,
        name: "오량액",
        parentId: 1,
        price: 300000,
        salePrice: 250000,
        order: 4
      },
      {
        id: 15,
        name: "탕수육",
        parentId: 4,
        price: 20000,
        salePrice: 18000,
        order: 0
      },
      {
        id: 12,
        name: "볶음밥",
        parentId: 3,
        price: 8000,
        salePrice: null,
        order: 11
      },
      {
        id: 3,
        name: "식사",
        parentId: null,
        price: null,
        salePrice: null,
        order: 1
      },
      {
        id: 16,
        name: "양장피",
        parentId: 4,
        price: 25000,
        salePrice: null,
        order: 10
      },
      {
        id: 14,
        name: "깐풍기",
        parentId: 4,
        price: 25000,
        salePrice: null,
        order: 8
      },
      {
        id: 2,
        name: "음료",
        parentId: null,
        price: null,
        salePrice: null,
        order: 3
      },
      {
        id: 8,
        name: "수정방",
        parentId: 1,
        price: 300000,
        salePrice: null,
        order: 5
      },
      {
        id: 1,
        name: "주류",
        parentId: null,
        price: null,
        salePrice: null,
        order: 3
      },
      {
        id: 11,
        name: "짜장면",
        parentId: 3,
        price: 7000,
        salePrice: null,
        order: 7
      },
      {
        id: 5,
        name: "이과두주",
        parentId: 1,
        price: 3000,
        salePrice: null,
        order: 14
      },
      {
        id: 4,
        name: "요리",
        parentId: null,
        price: null,
        salePrice: null,
        order: 2
      },
      {
        id: 10,
        name: "사이다",
        parentId: 2,
        price: 2000,
        salePrice: null,
        order: 13
      }
    ]
  })
};
</script>

<style>
@import url("../assets/css/reset.css");

#menus #menu1 {
  z-index: 10;
}
</style>
