<template>
  <div v-if="!item.hidden">
    <template v-if="hasOneShowingChild(item.children,item) && (!onlyOneChild.children||onlyOneChild.noShowingChildren)&&!item.alwaysShow">
      <app-link v-if="onlyOneChild.meta" :to="resolvePath(onlyOneChild.path)">
        <el-menu-item :index="resolvePath(onlyOneChild.path)" :class="{'submenu-title-noDropdown':!isNest}">
          <!-- :icon="onlyOneChild.meta.icon||(item.meta&&item.meta.icon)"  -->
          <!-- 二级菜单标题 -->
          <!-- <i v-if="item.path != '/'" style="margin-right: 10px" class="iconfont" :class="[item.meta && item.meta.icon ? `${item.meta.icon}`: '']" /> -->
          <i v-if="item.path == '/'" class="iconfont" :class="[onlyOneChild.meta && onlyOneChild.meta.icon ? `${onlyOneChild.meta.icon}`: '']" />
          <item :title="generateTitle(onlyOneChild.meta.title)" />
        </el-menu-item>
      </app-link>
    </template>
    <el-submenu v-else ref="subMenu" :index="resolvePath(item.path)" popper-append-to-body>
      <template slot="title">
        <!-- :icon="item.meta && item.meta.icon" -->
        <!-- 一级菜单标题 -->
        <i class="iconfont" :class="[item.meta && item.meta.icon ? `${item.meta.icon}`: '']" />
        <item v-if="item.meta" :title="generateTitle(item.meta.title)" />
      </template>
      <sidebar-item
        v-for="child in item.children"
        :key="child.path"
        :is-nest="true"
        :item="child"
        :base-path="resolvePath(child.path)"
        class="nest-menu"
      />
    </el-submenu>
  </div>
</template>

<script>
import path from 'path'
import { generateTitle } from '@/utils/i18n'
import { isExternal } from '@/utils/validate'
import Item from './Item'
import AppLink from './Link'
import FixiOSBug from './FixiOSBug'
import { mapGetters } from 'vuex'

export default {
  name: 'SidebarItem',
  components: { Item, AppLink },
  mixins: [FixiOSBug],
  props: {
    // route object
    item: {
      type: Object,
      required: true
    },
    isNest: {
      type: Boolean,
      default: false
    },
    basePath: {
      type: String,
      default: ''
    }
  },
  data() {
    // To fix https://github.com/PanJiaChen/vue-admin-template/issues/237
    // TODO: refactor with render function
    this.onlyOneChild = null
    return {
      isActive: false
    }
  },
  computed: {
    ...mapGetters([
      'permission_routes'
    ])
  },
  mounted() {
  },
  methods: {
    hasOneShowingChild(children = [], parent) {
      const showingChildren = children.filter(item => {
        if (item.hidden) {
          return false
        } else {
          // Temp set(will be used if only has one showing child)
          this.onlyOneChild = item
          return true
        }
      })

      // When there is only one child router, the child router is displayed by default
      if (showingChildren.length === 1) {
        return true
      }

      // Show parent if there are no child router to display
      if (showingChildren.length === 0) {
        this.onlyOneChild = { ... parent, path: '', noShowingChildren: true }
        return true
      }

      return false
    },
    resolvePath(routePath) {
      if (isExternal(routePath)) {
        return routePath
      }
      if (isExternal(this.basePath)) {
        return this.basePath
      }
      /* console.log(path.resolve(this.basePath, routePath))*/
      return path.resolve(this.basePath, routePath)
    },
    generateTitle
  }
}
</script>
<style lang="scss" scoped>
  .icon-circle {
    display: inline-block;
    width: 12px;
    height: 12px;
    border-radius: 12px;
    background: #0f58ff;
    margin-right: 10px;
  }
</style>
