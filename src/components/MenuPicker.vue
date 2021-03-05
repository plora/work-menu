<template>
  <section :id="menuId" class="select-menu">
    <div class="list-menu" @click.prevent="onShowMenu">
      {{ filterList | filterMenu }}
    </div>
    <div class="list-menu-wrap" v-show="isMenu">
      <div
        class="parent-menu"
        v-for="(menus, parent) in menuList"
        :key="parent"
      >
        <label :for="`${menuId}-${menus.id}`">
          <input
            type="checkbox"
            :id="`${menuId}-${menus.id}`"
            v-model="menusCheck"
            :value="menus.name"
            @change="onClickParent"
          />
          {{ menus.name }}
        </label>
        <div
          class="children-menu"
          v-for="(menu, children) in menus.children"
          :key="children"
        >
          <label :for="`${menuId}-${menu.id}`">
            <input
              type="checkbox"
              :id="`${menuId}-${menu.id}`"
              v-model="menuCheck"
              :value="menu.name"
              v-show="!disableChild"
              @change="onClickChildren"
            />
            {{ menu.name }}
            <span
              v-if="menu.price"
              class="price"
              :class="[menu.salePrice ? 'discount' : '']"
              >{{ menu.price | filterPrice }} 원</span
            >
            <span v-if="menu.salePrice" class="sale"
              >{{ menu.salePrice | filterPrice }} 원</span
            >
          </label>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: "Home",
  props: {
    menus: {
      type: Array
    },
    disableChild: {
      type: Boolean
    },
    menuId: {
      type: String,
      default: "menu"
    }
  },
  data: () => ({
    menuList: [],
    menuCheck: [],
    menusCheck: [],
    filterList: [],
    isMenu: false
  }),
  filters: {
    filterMenu(list) {
      return list.join(",") || list.join();
    },
    filterPrice(list) {
      return list.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    }
  },
  created() {
    this.menuList = this.setDefaultList();
  },
  methods: {
    initMenuList(menuList) {
      return menuList.map(list => {
        list.checked = false;
        if (list.children && list.children.length) {
          this.initMenuList(list.children);
        }
        return list;
      });
      // return menuList.reduce((acc, list) => {
      //   list.checked = false;
      //   if (list.children && list.children.length) {
      //     acc = [...acc, ...this.setFilterList(list.children)];
      //   }
      //   return acc;
      // }, []);
    },
    setDefaultList() {
      console.log(this.menus);
      const menuList = this.menus.reduce((acc, menu) => {
        if (menu.parentId === null) {
          const children = this.menus
            .filter(list => list.parentId === menu.id)
            .map(item => {
              item.checked = false;
              return item;
            });
          menu.checked = false;
          menu.children = [...children];
          acc.push(menu);
        }
        return acc;
      }, []);
      console.log(menuList);
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
    setFilterList(menuList) {
      return menuList.reduce((acc, list) => {
        if (list.checked) {
          acc = [...acc, list.name];
        }
        if (list.children && list.children.length) {
          acc = [...acc, ...this.setFilterList(list.children)];
        }
        return acc;
      }, []);
    },
    onShowMenu() {
      this.isMenu = !this.isMenu;
    },
    onClickParent() {
      this.menuList = this.initMenuList(this.menuList);
      if (this.menusCheck && this.menusCheck.length) {
        const activeMenu = this.menusCheck
          .reduce((acc, menu) => {
            const result = this.menuList.filter(list => list.name === menu);
            acc.push(...result);
            return acc;
          }, [])
          .map(item => {
            item.checked = true;
            return [
              ...item.children.map(item => {
                item.checked = true;
                return item.name;
              })
            ];
          });
        console.log(activeMenu, "menu");
        this.menuCheck = activeMenu.join(",").split(",");
        this.filterList = [];
        this.filterList = this.setFilterList(this.menuList);
      } else {
        this.menusCheck = [];
        this.menuCheck = [];
        this.filterList = [];
        // this.menuList = this.initMenuList(this.menuList);
      }
    },
    onClickChildren() {
      this.menuList = this.initMenuList(this.menuList);
      if (this.menuCheck && this.menuCheck.length) {
        const activeMenu = this.menuCheck
          .reduce((acc, menu) => {
            console.log(name);
            const result = this.menus.filter(list => list.name === menu);
            console.log(result, "menu");
            acc.push(...result);
            return acc;
          }, [])
          .map(item => {
            item.checked = true;
            return item.name;
          });
        console.log(activeMenu);
        // this.menusCheck = activeMenu.join(",").split(",");
        this.filterList = [];
        this.filterList = this.setFilterList(this.menuList);
      } else {
        this.menusCheck = [];
        this.menuCheck = [];
        this.filterList = [];
        // this.menuList = this.initMenuList(this.menuList);
      }
    }
  }
};
</script>

<style>
label {
  display: block;
  width: 100%;
  cursor: pointer;
}
.select-menu {
  position: relative;
  margin: 1em 1em;
  text-align: left;
  z-index: 0;
}
.list-menu {
  width: 100%;
  height: 2.2em;
  line-height: 1em;
  padding: 0.5em 0.5em;
  border: 1px solid #aaa;
  border-radius: 5px;
  cursor: pointer;
}
.list-menu-wrap {
  position: absolute;
  left: 0;
  top: 2.2em;
  width: 100%;
  padding: 1em;
  border: 1px solid #aaa;
  border-radius: 5px;
  box-shadow: 5px 10px 18px #ddd;
  background: #fff;
}
.parent-menu {
  padding: 5px 10px;
  text-align: left;
}
.children-menu {
  padding: 5px 10px;
}
.price {
  margin-right: 1em;
  color: cadetblue;
}
.price.discount {
  color: #ddd;
  text-decoration: line-through;
}
.sale {
  color: crimson;
}
</style>
