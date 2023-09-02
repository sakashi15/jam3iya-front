<template>
  <aside
    class="left-sidebar text-right"
    :class="Sidebar_drawer ? `show-sidebar bg-${SidebarColor}` : `bg-${SidebarColor}`"
  >
    <span
        class="d-block d-lg-none close-sidebar-btn mt-1"
        @click="showMobileSidebar"
    ><i class="ti ti-close"></i
    ></span>
    <br class="d-lg-none">
    <div :class="`bg-${logoColor} navbar-header`">

      <LogoDark v-if="logoColor == 'white'" />
      <LogoLight v-else />

    </div>
    
    <VuePerfectScrollbar class="scrlbar mt-3" >
      <ul class="nav flex-column mb-0 pr-0">
        <template v-for="(route, i) in routes">
          <!---Single Menu -->
          <li class="nav-item ml-2" :key="i" v-if="!route.childern && !route.header">
            <router-link :to="`${route.to}`" class="nav-link d-flex">
              <feather :type="route.icon"></feather>
              <span class="hide-text">{{ $t(route.i18n) || route.title }}</span>
            </router-link>
          </li>
        </template>
      </ul>
    </VuePerfectScrollbar>
  </aside>
</template>

<script>
import LogoDark from "../logo-dark/LogoDark";
import LogoLight from "../logo-light/LogoLight";
import { mapState } from "vuex";
import SidebarData from "./SidebarData";
import VuePerfectScrollbar from 'vue-perfect-scrollbar'

export default {
  name: "VerticalSidebar",
  components: {
    LogoDark,
    LogoLight,
    VuePerfectScrollbar
  },
  data: () => ({
    initialShow: true,
    routes: SidebarData,
    
  }),
  computed: {
    ...mapState(["logoColor", "Sidebar_drawer", "SidebarColor"]),
    check(){
      return console.log(this.$router.history.current);
    }
  },
  methods: {
    showMobileSidebar() {
      this.$store.commit("SET_SIDEBAR_DRAWER", false);
      
    },
  }
};
</script>

<style>
</style>