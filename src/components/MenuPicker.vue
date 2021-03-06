<template>
  <section :id="menuId" class="select-menu">
    <div
      class="list-menu menu-arrow"
      @click.prevent="onShowMenu"
      :class="isMenu | filterArrow"
    >
      {{ filterList | filterMenu }}
    </div>
    <div class="list-menu-wrap" v-show="isMenu">
      <div
        class="parent-menu"
        v-for="(menus, parent) in menuList"
        :key="parent"
      >
        <label :for="`${menuId}-${menus.id}`" class="cursor">
          <input
            type="checkbox"
            :id="`${menuId}-${menus.id}`"
            v-model="menusCheck"
            :value="menus.name"
            @change="onClickParent(menus.name)"
          />
          {{ menus.name }}
        </label>
        <div
          class="children-menu"
          v-for="(menu, children) in menus.children"
          :key="children"
        >
          <label
            :for="`${menuId}-${menu.id}`"
            :class="disableChild | filterDisable"
          >
            <input
              type="checkbox"
              :id="`${menuId}-${menu.id}`"
              v-model="menuCheck"
              :value="menu.name"
              v-if="!disableChild"
              @change="onClickChildren(menu.name)"
            />
            {{ menu.name }}
            <span
              v-if="menu.price"
              class="price"
              :class="menu.salePrice | filterDiscount"
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
  name: "MenuPicker",
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
    },
    filterDisable(disable) {
      return disable ? "" : "cursor";
    },
    filterArrow(arrow) {
      return arrow ? "select-up" : "select-down";
    },
    filterDiscount(price) {
      return price ? "discount" : "";
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
    },
    setDefaultList() {
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
    setMenuCheck() {
      const activeMenu = this.menuCheck
        .reduce((acc, menu) => {
          const result = this.menus.filter(list => list.name === menu);
          acc.push(...result);
          return acc;
        }, [])
        .map(item => {
          item.checked = true;
          return item.name;
        });
      const menu = new Set([
        ...this.menusCheck,
        ...activeMenu.join(",").split(",")
      ]);
      this.menusCheck = [...menu];
      this.filterList = this.setFilterList(this.menuList);
    },
    onShowMenu() {
      this.isMenu = !this.isMenu;
    },
    onClickParent(parentName) {
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
            if (item.name === parentName) {
              return [
                ...item.children.map(item => {
                  return item.name;
                })
              ];
            } else {
              return [
                ...item.children.map(item => {
                  if (this.disableChild) {
                    return item.name;
                  }
                  if (item.checked) {
                    return item.name;
                  }
                })
              ];
            }
          });
        if (this.disableChild) {
          this.menuCheck = [];
        }
        let menu = new Set([
          ...this.menuCheck,
          ...activeMenu.join(",").split(",")
        ]);

        this.menuCheck = [...menu];
        this.filterList = [];
        this.filterList = this.setFilterList(this.menuList);
      }
      if (this.menuCheck && this.menuCheck.length) {
        this.setMenuCheck(parentName);
      }
    },
    onClickChildren(childrenName) {
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
          });

        const menu = new Set([
          ...this.menuCheck,
          ...activeMenu.join(",").split(",")
        ]);
        this.menuCheck = [...menu];
        this.filterList = [];
        this.filterList = this.setFilterList(this.menuList);
      }
      if (this.menuCheck && this.menuCheck.length) {
        this.setMenuCheck(childrenName);
      }
    }
  }
};
</script>

<style>
.cursor {
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
.menu-arrow {
  position: relative;
}
.menu-arrow:after {
  content: "▼";
  display: inline-block;
  position: absolute;
  right: 1em;
  top: 0.5em;
  line-height: 1em;
  transition: 0.3s linear;
}
.select-down:after {
  transform: rotate(0deg);
}
.select-up:after {
  transform: rotate(180deg);
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
  padding: 5px 1em;
  text-align: left;
}
.children-menu {
  padding: 5px 1.4em;
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
