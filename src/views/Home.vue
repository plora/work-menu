<template>
  <section>
    <input type="text" />
    <div class="parent-menu" v-for="(menus, parent) in menuList" :key="parent">
      <label :for="`menu${menus.id}`">
        <input
          type="checkbox"
          :id="`menu${menus.id}`"
          v-model="menusCheck"
          :value="menus.name"
          @change="onClickParent()"
        />
        {{ menus.name }}
      </label>
      <div
        class="children-menu"
        v-for="(menu, children) in menus.children"
        :key="children"
      >
        <label :for="`menu${menu.id}`">
          <input
            type="checkbox"
            :id="`menu${menu.id}`"
            v-model="menuCheck"
            :value="menu.name"
          />
          {{ menu.name }}
        </label>
      </div>
    </div>
  </section>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";

export default {
  name: "Home",
  components: {
    // HelloWorld
  },
  data: () => ({
    defaultList: [
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
    ],
    menuList: [],
    menuCheck: [],
    menusCheck: []
  }),
  created() {
    this.menuList = this.setDefaultList();
  },
  methods: {
    setDefaultList() {
      const menuList = this.defaultList.reduce((acc, menu) => {
        if (menu.parentId === null) {
          const children = this.defaultList.filter(
            list => list.parentId === menu.id
          );
          menu.children = [...children];
          acc.push(menu);
        }
        return acc;
      }, []);
      const resultList = this.setSortList(menuList);
      return resultList;
    },
    setSortList(list) {
      return list.sort((prev, next) => {
        if (next.children) {
          this.setSortList(next.children);
        }
        if (prev.children) {
          this.setSortList(prev.children);
        }
        const pOrder = prev.order;
        const nOrder = next.order;
        if (pOrder === nOrder) {
          return prev.id - next.id;
        }
        return pOrder - nOrder;
      });
    },
    onClickParent() {
      if (this.menusCheck && this.menusCheck.length) {
        const activeMenu = this.menusCheck
          .reduce((acc, menu) => {
            const result = this.menuList.filter(list => list.name === menu);
            acc.push(...result);
            return acc;
          }, [])
          .map(item => {
            return [...item.children.map(item => item.name)];
          });
        this.menuCheck = activeMenu.join(",").split(",");
      } else {
        this.menusCheck = [];
        this.menuCheck = [];
      }
    }
  }
};
</script>

<style scoped>
label {
  cursor: pointer;
}
.parent-menu {
  padding: 5px 10px;
  text-align: left;
}
.children-menu {
  padding: 5px 10px;
}
</style>
