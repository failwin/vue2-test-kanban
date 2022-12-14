<template>
  <div
    class="my-sidebar"
    :class="{
      'my-sidebar__hidden': !isDesktopSidebarVisible,
      'my-sidebar__showed': isMobileSidebarVisible,
    }"
  >
    <div class="my-sidebar_spacer"></div>
    <div class="my-sidebar_content">
      <div class="my-sidebar_total h4">
        ALL BOARDS ({{ dashboards.length }})
      </div>
      <div class="my-sidebar_menu-scroll">
        <ul class="my-sidebar_menu">
          <li
            class="my-sidebar_menu-item"
            :class="{
              'my-sidebar_menu-item__active':
                activeDashboard && activeDashboard.id === dashboard.id,
            }"
            v-for="dashboard of dashboards"
            :key="dashboard.id"
          >
            <button
              class="my-sidebar_menu-button"
              @click="onSidebarItemClick(dashboard.id)"
            >
              <my-board-icon class="my-sidebar_menu-item-icon" />
              <span class="my-sidebar_menu-item-text h3">{{
                dashboard.name
              }}</span>
            </button>
          </li>
          <li class="my-sidebar_menu-item my-sidebar_menu-item__new">
            <button
              class="my-sidebar_menu-button"
              @click.prevent="onAddNewDashboardClick"
            >
              <my-board-icon class="my-sidebar_menu-item-icon" />
              <span class="my-sidebar_menu-item-text h3">
                + Create New Board
              </span>
            </button>
          </li>
        </ul>
      </div>
      <div>
        <div
          class="my-sidebar_theme-toggle"
          :class="{ 'my-sidebar_theme-toggle__black': theme === 'black' }"
        >
          <my-sun-icon />
          <button @click="toggleTheme" class="my-sidebar_theme-buttom">
            theme switch
          </button>
          <my-moon-icon />
        </div>

        <ul class="my-sidebar_menu my-sidebar_menu__toggler">
          <li class="my-sidebar_menu-item">
            <button
              class="my-sidebar_menu-button"
              @click="toggleDesktopSidebar"
            >
              <my-eye-close-icon class="my-sidebar_menu-item-icon" />
              <span class="my-sidebar_menu-item-text h3">Hide Sidebar</span>
            </button>
          </li>
        </ul>
      </div>
    </div>
    <div class="my-sidebar_toggle-show">
      <my-button
        class="my-sidebar_toggle-button"
        size="large"
        @click="toggleDesktopSidebar"
      >
        <my-eye-open-icon class="my-sidebar_menu-item-icon" />
      </my-button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { mapGetters, mapActions } from "vuex";
import { MyButton } from "@/components/form/MyButton";
import MyBoardIcon from "@/components/icons/MyBoardIcon.vue";
import MyEyeCloseIcon from "@/components/icons/MyEyeCloseIcon.vue";
import MyEyeOpenIcon from "@/components/icons/MyEyeOpenIcon.vue";
import MySunIcon from "@/components/icons/MySunIcon.vue";
import MyMoonIcon from "@/components/icons/MyMoonIcon.vue";
import { MODAL_DASHBOARD_EDIT } from "@/store/constants";
import type { Dashboard } from "@/store/types";
import type { PropType } from "vue";

type Size = "small" | "large";

type Color = "primary" | "secondary" | "destructive";

type MySidebarProps = {
  size?: Size;
  color?: Color;

  onAddNewDashboardClick: () => void;
  isDesktopSidebarVisible: boolean;
  isMobileSidebarVisible: boolean;
  theme: string;
  dashboards: Dashboard[];
  activeDashboard: Dashboard | undefined;

  setActiveDashboard: (id: string) => void;
  toggleDesktopSidebar: () => void;
  toggleMobileSidebar: (force?: boolean) => void;
  toggleTheme: () => void;
  showModal: (opts: { name: string }) => void;

  onSidebarItemClick: (dashboardId: string) => void;
};

export default defineComponent<MySidebarProps, MySidebarProps>({
  name: "MySidebar",
  components: {
    MyButton,
    MyBoardIcon,
    MyEyeCloseIcon,
    MyEyeOpenIcon,
    MySunIcon,
    MyMoonIcon,
  },
  props: {
    size: {
      type: String as PropType<Size>,
      required: false,
      default: "small",
    },
    color: {
      type: String as PropType<Color>,
      required: false,
      default: "primary",
    },
  },
  computed: {
    ...mapGetters({
      isDesktopSidebarVisible: "sidebar/isDesktopSidebarVisible",
      isMobileSidebarVisible: "sidebar/isMobileSidebarVisible",
      theme: "theme/activeTheme",
      dashboards: "dashboards/dashboards",
      activeDashboard: "dashboards/activeDashboard",
    }),
  },
  methods: {
    ...mapActions({
      toggleDesktopSidebar: "sidebar/toggleDesktopSidebar",
      toggleMobileSidebar: "sidebar/toggleMobileSidebar",
      toggleTheme: "theme/toggleTheme",
      showModal: "modals/showModal",
      setActiveDashboard: "dashboards/setActiveDashboard",
    }),
    onAddNewDashboardClick() {
      this.showModal({
        name: MODAL_DASHBOARD_EDIT,
      });
      this.toggleMobileSidebar(false);
    },
    onSidebarItemClick(dashboardId: string) {
      this.setActiveDashboard(dashboardId);
      this.toggleMobileSidebar(false);
    },
  },
});
</script>

<style>
.my-sidebar {
}

.my-sidebar_content {
  position: fixed;
  top: var(--header-height);
  bottom: 0;
  left: 0;
  display: flex;
  flex-direction: column;
  width: var(--sidebar-width);
  padding-bottom: 33px;
  background: var(--sidebar-bg-color);
  border-right: solid 1px var(--sidebar-border-color);

  transition: left 0.3s ease;
  z-index: var(--z-index-sidebar-show);
}

.my-sidebar_spacer {
  width: var(--sidebar-width);

  transition: width 0.3s ease;
}

.my-sidebar_total {
  flex-shrink: 0;
  padding: 19px 32px;
  color: var(--sidebar-menu-color);
}

.my-sidebar_menu-scroll {
  flex-grow: 1;
  overflow: auto;
}

.my-sidebar_menu {
  margin: 0 25px 0 0;
  padding: 0;
  list-style: none;
}

.my-sidebar_menu-item {
}

.my-sidebar_menu-button {
  display: flex;
  width: 100%;
  align-items: center;
  padding: 14px 20px 15px 32px;
  box-sizing: border-box;
  border: none;
  background: none;
  border-radius: 0 100px 100px 0;
  color: var(--sidebar-menu-color);
  cursor: pointer;
  text-decoration: none;

  transition: background 0.3s ease;
}
.my-sidebar_menu-button:hover {
  color: var(--sidebar-menu-color-hover);
  background: var(--sidebar-menu-bg-color-hover);
}
.my-sidebar_menu-item__active .my-sidebar_menu-button {
  cursor: default;
}
.my-sidebar_menu-item__active .my-sidebar_menu-button,
.my-sidebar_menu-item__active .my-sidebar_menu-button:hover {
  color: var(--sidebar-menu-color-active);
  background: var(--sidebar-menu-bg-color-active);
}
.my-sidebar_menu-item__new .my-sidebar_menu-button {
  color: var(--sidebar-menu-color-hover);
}

.my-sidebar_menu-item-icon {
  flex-shrink: 0;
  margin-right: 14px;
}

.my-sidebar_menu-item-text {
}

.my-sidebar_toggle-show {
  position: fixed;
  bottom: 33px;
  left: 0;
  z-index: var(--z-index-sidebar);
}

.my-sidebar_toggle-button {
  width: 54px;
  padding-left: 18px;
  padding-right: 18px;
  border-radius: 0 24px 24px 0;
}

.my-sidebar_theme-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 10px 25px;
  padding: 15px;
  background: var(--theme-toggle-bg-color);
  color: var(--theme-toggle-color);
  border-radius: 6px;
}

.my-sidebar_theme-buttom {
  position: relative;
  display: block;
  width: 38px;
  height: 19px;
  margin: 0 25px;
  border-radius: 10px;
  background: var(--theme-toggle-button-bg-color);
  border: none;
  cursor: pointer;
  overflow: hidden;
  white-space: nowrap;
  text-indent: 100px;
}
.my-sidebar_theme-buttom:after {
  content: "";
  position: absolute;
  top: 3px;
  left: 4px;
  display: block;
  width: 13px;
  height: 13px;
  border-radius: 50%;
  background: var(--theme-toggle-button-color);

  transition: transform 0.3s ease;
}
.my-sidebar_theme-toggle__black .my-sidebar_theme-buttom:after {
  transform: translateX(17px);
}

.my-sidebar__hidden .my-sidebar_content {
  left: var(--sidebar-width-n);
}

.my-sidebar__hidden .my-sidebar_spacer {
  width: 0;
}

/* Mobile */
@media (max-width: 700px) {
  .my-sidebar {
    position: fixed;
    top: var(--header-height);
    bottom: 0;
    left: 0;
    right: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start;
    max-height: 100%;
    padding: 16px 40px;
    visibility: hidden;
    opacity: 0;

    transition: opacity 0.1s ease;
  }

  .my-sidebar_content {
    position: relative;
    top: 0;
    max-height: calc(100% - 32px);
    width: 100%;
    padding-bottom: 5px;
    max-width: var(--sidebar-width);
    border-right: none;
    border-radius: 8px;
    transform: translateY(10px);

    transition: transform 0.1s ease;
  }
  .my-sidebar__hidden .my-sidebar_content {
    left: 0;
  }

  .my-sidebar_spacer {
    position: absolute;
    inset: 0;
    width: 100%;
    background: var(--modal-overlay-bg-color);
    opacity: 0.5;
  }
  .my-sidebar__hidden .my-sidebar_spacer {
    width: 100%;
  }

  .my-sidebar_total {
    padding: 19px 15px;
  }

  .my-sidebar_menu-button {
    padding: 14px 15px 15px 15px;
  }

  .my-sidebar_theme-toggle {
    margin: 10px 15px;
  }

  .my-sidebar_menu__toggler,
  .my-sidebar_toggle-show {
    display: none;
  }

  .my-sidebar__showed {
    visibility: visible;
    opacity: 1;
  }

  .my-sidebar__showed .my-sidebar_content {
    transform: translateY(0px);
  }
}
</style>
