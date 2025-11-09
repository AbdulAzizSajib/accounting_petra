<template>
  <a-layout has-sider class="finance-sidebar">
    <!-- Sidebar -->
    <a-layout-sider ref="siderRef" :collapsed="collapsed" :width="sidebarWidth" collapsed-width="80" breakpoint="lg"
      :trigger="null" collapsible :style="{
        overflowY: 'auto',
        height: '100vh',
        position: 'fixed',
        left: 0,
        top: 0,
        zIndex: 999,
        background: 'linear-gradient(180deg, #0a1929 0%, #001e3c 100%)',
        boxShadow: '4px 0 24px rgba(0, 0, 0, 0.12)',
      }">

      <!-- Logo Section -->
      <div class="logo-container">
        <router-link :to="{ name: 'overview' }" class="logo-link">
          <div class="logo-wrapper">
            <img class="logo-image" src="@/assets/images/finance.png" alt="Finance Logo">
            <transition name="fade">
              <span v-if="!collapsed" class="logo-text">Finance Pro</span>
            </transition>
          </div>
        </router-link>
      </div>

      <!-- Menu -->
      <a-menu :items="items" :openKeys="state.openKeys" :selectedKeys="state.selectedKeys"
        @openChange="(keys) => (state.openKeys = keys)" theme="dark" mode="inline" class="custom-menu" />

      <!-- Sidebar Footer -->
      <div v-if="!collapsed" class="sidebar-footer">
        <div class="footer-info">
          <div class="footer-time">{{ time }}</div>
          <div class="footer-date">{{ date }}</div>
        </div>
      </div>
    </a-layout-sider>

    <a-layout :style="{ marginLeft: collapsed ? '80px' : `${sidebarWidth}px`, transition: 'all 0.3s ease' }">
      <!-- Header -->
      <Header :collapsed="collapsed" @update:collapsed="collapsed = $event" />

      <!-- Content -->
      <a-layout-content>
        <div :style="{
          margin: '0 16px',
          padding: $route?.name === 'home' ? '16px 0' : '16px 32px',
          background: $route?.name === 'home' ? 'transparent' : '#fff',
          borderRadius: $route?.name === 'home' ? '0' : '30px',
          boxShadow: $route?.name === 'home' ? 'none' : '0 4px 20px rgba(0, 0, 0, 0.08)',
          height: 'calc(100vh - 156px)',
          overflowY: 'auto',
          transition: 'all 0.3s ease',
        }">
          <slot></slot>
        </div>
      </a-layout-content>

      <!-- Footer -->
      <a-layout-footer :style="{
        background: 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)',
        padding: '16px 32px',
        margin: '16px 16px 8px',
        height: 'fit-content',
        lineHeight: 'normal',
        borderRadius: '30px',
        boxShadow: '0 4px 20px rgba(102, 126, 234, 0.3)',
        textAlign: 'center',
        color: '#fff',
        fontWeight: '500',
      }">
        All Reserved &copy; {{ new Date().getFullYear() }} | Finance Pro
      </a-layout-footer>
    </a-layout>
  </a-layout>
</template>

<script setup>
import { ref, h, onMounted, watch, reactive, nextTick } from "vue";
import {
  UserOutlined,
  FileTextOutlined,
  ContainerOutlined,
  DashboardOutlined,
  TransactionOutlined,
  FileSearchOutlined,
  BarChartOutlined,
} from "@ant-design/icons-vue";
import { useRoute, useRouter } from "vue-router";
import Header from "./header.vue";

const collapsed = ref(false);
const siderRef = ref(null);
const route = useRoute();
const router = useRouter();

const sidebarWidth = 280;
let time = ref();
let date = ref();

let timeOptions = {
  hour: "2-digit",
  minute: "2-digit",
  hour12: true,
};

let dateOptions = {
  month: "short",
  day: "2-digit",
  year: "numeric",
};

function updateDateTime() {
  let now = new Date();
  time.value = now.toLocaleString("en-US", timeOptions);
  date.value = now.toLocaleString("en-US", dateOptions);
}

onMounted(() => {
  updateDateTime();
  setInterval(updateDateTime, 60000);
});

window.addEventListener("keydown", (event) => {
  if (event?.key === "F3") {
    event.preventDefault();
    router.push({ name: "sales" });
  }
});

const state = reactive({
  openKeys: [],
  selectedKeys: [route.path],
});

const items = reactive([
  {
    key: "/finance-dashboard",
    label: "Dashboard",
    icon: () => h(DashboardOutlined),
    onClick: () => router.push("/finance-dashboard"),
  },
  
  {
    key: "files",
    label: "Files",
    icon: () => h(FileTextOutlined),
    children: [
      {
        key: "/files/group-code",
        label: "Group Code",
        onClick: () => router.push("/files/group-code"),
      },
      {
        key: "/files/ac-type",
        label: "A/C Type",
        onClick: () => router.push("/files/ac-type"),
      },
      {
        key: "/files/search-chart-of-accounts",
        label: "Chart of Accounts",
        onClick: () => router.push("/files/search-chart-of-accounts"),
      },
      {
        key: "/files/sub-ledger-category",
        label: "Sub-Ledger Category",
        onClick: () => router.push("/files/sub-ledger-category"),
      },
      {
        key: "/files/sub-ledger",
        label: "Sub Ledger",
        onClick: () => router.push("/files/sub-ledger"),
      },
      {
        key: "/files/opening",
        label: "Opening",
        onClick: () => router.push("/files/opening"),
      },
      {
        key: "/files/financial-month",
        label: "Financial Month",
        onClick: () => router.push("/files/financial-month"),
      },
      {
        key: "/files/set-period",
        label: "Set Period",
        onClick: () => router.push("/files/set-period"),
      },
    ],
  },
  {
    key: "transaction",
    label: "Transaction",
    icon: () => h(TransactionOutlined),
    children: [
      {
        key: "/transaction/voucher-list",
        label: "Voucher Entry",
        onClick: () => router.push("/transaction/voucher-list"),
      },
      {
        key: "/transaction/voucher-date-change",
        label: "Voucher Data Change",
        onClick: () => router.push("/transaction/voucher-date-change"),
      },
      {
        key: "/transaction/delete-voucher",
        label: "Delete Voucher",
        onClick: () => router.push("/transaction/delete-voucher"),
      },
      {
        key: "/transaction/voucher-print",
        label: "Voucher Print",
        onClick: () => router.push("/transaction/voucher-print"),
      },
      {
        key: "/transaction/voucher-search-chart-of-accounts",
        label: "Search Chart of Accounts",
        onClick: () => router.push("/transaction/voucher-search-chart-of-accounts"),
      },
    ],
  },
  {
    key: "/reports",
    label: "Reports",
    icon: () => h(BarChartOutlined),
    children: [
      {
        key: "/reports/journal-book-report",
        label: "Journal Book",
        onClick: () => router.push("/reports/journal-book-report"),
      },
      {
        key: "/reports/search-voucher-report",
        label: "Search Voucher",
        onClick: () => router.push("/reports/search-voucher-report"),
      },
      {
        key: "/reports/ledger-report",
        label: "Ledger",
        onClick: () => router.push("/reports/ledger-report"),
      },
      {
        key: "/reports/chart-of-accounts-reports",
        label: "Chart of Accounts",
        onClick: () => router.push("/reports/chart-of-accounts-reports"),
      },
      {
        key: "/reports/trial-balance",
        label: "Trial Balance",
        onClick: () => router.push("/reports/trial-balance"),
      },
    ],
  },
  {
    key: "user",
    label: "User Management",
    icon: () => h(UserOutlined),
    children: [
      {
        key: "/user",
        label: "User",
        onClick: () => router.push("/user"),
      },
      {
        key: "/role",
        label: "Role",
        onClick: () => router.push("/role"),
      },
      {
        key: "/permission",
        label: "Permission",
        onClick: () => router.push("/permission"),
      },
    ],
  },
]);

const parentOf = new Map();

function flattenItems(list, out = []) {
  list.forEach((it) => {
    out.push(it);
    if (it.children?.length) flattenItems(it.children, out);
  });
  return out;
}

function indexItems(list, parentKey = null) {
  list.forEach((it) => {
    if (parentKey) parentOf.set(it.key, parentKey);
    if (it.children?.length) indexItems(it.children, it.key);
  });
}

indexItems(items);

watch(
  () => route.path,
  async (newPath) => {
    const flat = flattenItems(items);
    const exact = flat.find((i) => i.key === newPath);
    const prefix = flat.find(
      (i) => typeof i.key === "string" && newPath.startsWith(i.key)
    );
    const matchedKey = (exact || prefix)?.key || newPath;
    const parentKey = parentOf.get(matchedKey) || null;

    state.selectedKeys = [matchedKey];
    state.openKeys = parentKey ? [parentKey] : [];

    await nextTick();
    const siderEl = siderRef.value?.$el;
    const selected = siderEl?.querySelector(".ant-menu-item-selected");
    if (selected && siderEl) {
      selected.scrollIntoView({ behavior: "smooth", block: "center" });
    }
  },
  { immediate: true }
);
</script>

<style scoped>
/* Logo Container */
.logo-container {
  padding: 24px 20px;
  margin-bottom: 16px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.logo-link {
  text-decoration: none;
  display: block;
}

.logo-wrapper {
  display: flex;
  align-items: center;
  gap: 16px;
  transition: all 0.3s ease;
}

.logo-image {
  width: 48px;
  height: 48px;
  object-fit: contain;
  filter: drop-shadow(0 4px 8px rgba(66, 153, 225, 0.4));
  transition: transform 0.3s ease;
}

.logo-wrapper:hover .logo-image {
  transform: scale(1.1) rotate(5deg);
}

.logo-text {
  font-size: 20px;
  font-weight: 700;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  letter-spacing: 0.5px;
}

/* Sidebar Footer */
.sidebar-footer {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 20px;
  background: linear-gradient(180deg, transparent 0%, rgba(0, 0, 0, 0.2) 100%);
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.footer-info {
  text-align: center;
  color: rgba(255, 255, 255, 0.7);
}

.footer-time {
  font-size: 18px;
  font-weight: 600;
  color: #fff;
  margin-bottom: 4px;
}

.footer-date {
  font-size: 12px;
  color: rgba(255, 255, 255, 0.6);
}

/* Fade Transition */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>

<style>
/* Custom Menu Styles */
.finance-sidebar .custom-menu {
  background: transparent !important;
  border-right: none !important;
  padding: 0 12px 120px 12px;
}

.finance-sidebar .ant-menu-item,
.finance-sidebar .ant-menu-submenu-title {
  height: fit-content !important;
  line-height: 1.5 !important;
  margin: 6px 0 !important;
  padding: 12px 20px !important;
  border-radius: 12px !important;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1) !important;
  font-weight: 500 !important;
  color: rgba(255, 255, 255, 0.8) !important;
}

.finance-sidebar .ant-menu-item:hover,
.finance-sidebar .ant-menu-submenu-title:hover {
  background: rgba(102, 126, 234, 0.15) !important;
  color: #fff !important;
  transform: translateX(4px);
}

.finance-sidebar .ant-menu-item-selected {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%) !important;
  color: #fff !important;
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4) !important;
  font-weight: 600 !important;
  transform: translateX(4px);
}

.finance-sidebar .ant-menu-item-selected::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 4px;
  height: 60%;
  background: #fff;
  border-radius: 0 4px 4px 0;
}

.finance-sidebar .ant-menu-submenu-selected>.ant-menu-submenu-title {
  background: rgba(102, 126, 234, 0.2) !important;
  color: #fff !important;
  font-weight: 600 !important;
}

.finance-sidebar .ant-menu-sub {
  background: rgba(0, 0, 0, 0.2) !important;
  border-radius: 12px !important;
  margin: 4px 0 !important;
  padding: 8px 0 !important;
}

.finance-sidebar .ant-menu-sub .ant-menu-item {
  padding-left: 48px !important;
  margin: 2px 8px !important;
  font-size: 14px !important;
}

.finance-sidebar .ant-menu-sub .ant-menu-item::before {
  content: '';
  position: absolute;
  left: 28px;
  top: 50%;
  transform: translateY(-50%);
  width: 6px;
  height: 6px;
  background: rgba(255, 255, 255, 0.4);
  border-radius: 50%;
  transition: all 0.3s ease;
}

.finance-sidebar .ant-menu-sub .ant-menu-item-selected::before {
  background: #fff;
  transform: translateY(-50%) scale(1.3);
}

.finance-sidebar .ant-menu-title-content {
  display: flex;
  align-items: center;
  font-size: 15px;
}

.finance-sidebar .ant-menu-item-icon,
.finance-sidebar .ant-menu-submenu-title .ant-menu-item-icon {
  font-size: 18px !important;
  margin-right: 12px !important;
}

.finance-sidebar .ant-menu-submenu-arrow {
  color: rgba(255, 255, 255, 0.6) !important;
  transition: all 0.3s ease !important;
}

.finance-sidebar .ant-menu-submenu-open>.ant-menu-submenu-title .ant-menu-submenu-arrow {
  color: #fff !important;
  transform: rotate(180deg);
}

/* Scrollbar Styling */
.finance-sidebar .ant-layout-sider::-webkit-scrollbar {
  width: 6px;
}

.finance-sidebar .ant-layout-sider::-webkit-scrollbar-track {
  background: rgba(0, 0, 0, 0.1);
  border-radius: 10px;
}

.finance-sidebar .ant-layout-sider::-webkit-scrollbar-thumb {
  background: rgba(102, 126, 234, 0.4);
  border-radius: 10px;
  transition: background 0.3s ease;
}

.finance-sidebar .ant-layout-sider::-webkit-scrollbar-thumb:hover {
  background: rgba(102, 126, 234, 0.6);
}

/* Collapsed State */
.finance-sidebar .ant-layout-sider-collapsed .ant-menu-item,
.finance-sidebar .ant-layout-sider-collapsed .ant-menu-submenu-title {
  padding: 16px 20px !important;
  justify-content: center;
}

.finance-sidebar .ant-layout-sider-collapsed .ant-menu-item-icon {
  margin-right: 0 !important;
  font-size: 20px !important;
}

/* Animation for menu items */
@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateX(-10px);
  }

  to {
    opacity: 1;
    transform: translateX(0);
  }
}

.finance-sidebar .ant-menu-item,
.finance-sidebar .ant-menu-submenu {
  animation: slideIn 0.3s ease forwards;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .finance-sidebar .ant-layout-sider {
    box-shadow: 6px 0 32px rgba(0, 0, 0, 0.2) !important;
  }
}
</style>