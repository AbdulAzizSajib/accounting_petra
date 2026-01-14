<template>
  <MainLayout>
    <!-- Header with Title and Filters -->
    <div class="border-b border-black px-4 py-3 bg-white">
      <div class="flex items-center justify-between">
        <h1 class="text-xl font-bold text-black">Balance Sheet</h1>
        <div class="flex items-center gap-4">
          <div class="flex items-center gap-2">
            <label class="text-sm font-medium">From:</label>
            <input
              type="date"
              v-model="DateFrom"
              :max="DateTo"
              class="px-2 py-1 text-sm border border-black rounded"
            />
          </div>
          <div class="flex items-center gap-2">
            <label class="text-sm font-medium">To:</label>
            <input
              type="date"
              v-model="DateTo"
              :min="DateFrom"
              class="px-2 py-1 text-sm border border-black rounded"
            />
          </div>
          <a-button
            type="primary"
            size="small"
            @click="fetchBalanceSheet"
            :loading="loading"
            :disabled="dateError"
          >
            Preview
          </a-button>
          <button
            @click="exportPDF"
            :disabled="!hasData"
            class="text-black hover:text-gray-600 disabled:opacity-50"
            title="Export PDF"
          >
            <Icon icon="mdi:file-pdf-box" width="24" height="24" />
          </button>
          <button
            @click="exportExcel"
            :disabled="!hasData"
            class="text-black hover:text-gray-600 disabled:opacity-50"
            title="Export Excel"
          >
            <Icon icon="mdi:file-excel" width="24" height="24" />
          </button>
        </div>
      </div>
    </div>

    <!-- Company Header -->
    <div class="bg-white text-center py-2 border-b border-black">
      <span class="font-bold text-lg">{{ companyName }}</span>
      <p class="text-sm">as at {{ formatDisplayDate(DateTo) }}</p>
    </div>

    <!-- Loading State -->
    <div v-if="loading" class="flex justify-center items-center py-20 bg-white">
      <a-spin size="large" />
    </div>

    <!-- No Data Found State -->
    <div
      v-else-if="hasSearched && !hasData"
      class="flex flex-col items-center justify-center py-20 bg-white"
    >
      <Icon icon="tabler:file-off" class="text-6xl text-gray-400 mb-4" />
      <h2 class="text-xl font-semibold text-gray-600 mb-2">No Data Found</h2>
      <p class="text-gray-500 text-sm">
        No balance sheet data available for the selected period
      </p>
    </div>

    <!-- Balance Sheet Two-Column Layout -->
    <div v-else-if="hasData" class="balance-sheet-container bg-white min-h-[calc(100vh-200px)] flex flex-col border border-black">
      <!-- Main Content Area -->
      <div class="flex flex-1">
        <!-- Left Side - Liabilities -->
        <div class="w-1/2 border-r border-black flex flex-col">
          <!-- Column Header -->
          <div class="flex border-b border-black bg-gray-100">
            <div class="flex-1 px-3 py-2 font-bold text-black">Liabilities</div>
            <div class="w-32 px-3 py-2 text-right font-bold border-l border-black">Amount</div>
          </div>

          <!-- Liabilities Content -->
          <div class="flex-1">
            <!-- Capital Account / Owners' Equity -->
            <div class="px-3 py-1 font-bold text-sm border-b border-gray-300 bg-gray-50">
              Capital Account
            </div>
            <template v-if="f_balanceSheetData?.length > 0">
              <div
                v-for="(item, index) in f_balanceSheetData"
                :key="`f-${index}`"
                class="flex border-b border-gray-200"
              >
                <div class="flex-1 px-3 py-1 text-sm pl-6">{{ item?.ACType1Details }}</div>
                <div class="w-32 px-3 py-1 text-sm text-right border-l border-gray-200">{{ formatAmount(item?.Amount) }}</div>
              </div>
            </template>
            <div class="flex border-b border-gray-300">
              <div class="flex-1 px-3 py-1 text-sm font-semibold"></div>
              <div class="w-32 px-3 py-1 text-sm text-right font-semibold border-l border-gray-200">{{ formatAmount(f_total) }}</div>
            </div>

            <!-- Non-Current Liabilities -->
            <div class="px-3 py-1 font-bold text-sm border-b border-gray-300 bg-gray-50">
              Loans (Liability)
            </div>
            <template v-if="e_balanceSheetData?.length > 0">
              <div
                v-for="(item, index) in e_balanceSheetData"
                :key="`e-${index}`"
                class="flex border-b border-gray-200"
              >
                <div class="flex-1 px-3 py-1 text-sm pl-6">{{ item?.ACType1Details }}</div>
                <div class="w-32 px-3 py-1 text-sm text-right border-l border-gray-200">{{ formatAmount(item?.Amount) }}</div>
              </div>
            </template>
            <div class="flex border-b border-gray-300">
              <div class="flex-1 px-3 py-1 text-sm font-semibold"></div>
              <div class="w-32 px-3 py-1 text-sm text-right font-semibold border-l border-gray-200">{{ formatAmount(e_total) }}</div>
            </div>

            <!-- Current Liabilities -->
            <div class="px-3 py-1 font-bold text-sm border-b border-gray-300 bg-gray-50">
              Current Liabilities
            </div>
            <template v-if="d_balanceSheetData?.length > 0">
              <div
                v-for="(item, index) in d_balanceSheetData"
                :key="`d-${index}`"
                class="flex border-b border-gray-200"
              >
                <div class="flex-1 px-3 py-1 text-sm pl-6">{{ item?.ACType1Details }}</div>
                <div class="w-32 px-3 py-1 text-sm text-right border-l border-gray-200">{{ formatAmount(item?.Amount) }}</div>
              </div>
            </template>
            <div class="flex border-b border-gray-300">
              <div class="flex-1 px-3 py-1 text-sm font-semibold"></div>
              <div class="w-32 px-3 py-1 text-sm text-right font-semibold border-l border-gray-200">{{ formatAmount(d_total) }}</div>
            </div>

            <!-- Profit & Loss -->
            <div class="px-3 py-1 font-bold text-sm border-b border-gray-300 bg-gray-50">
              Profit & Loss A/c
            </div>
            <div class="flex border-b border-gray-200">
              <div class="flex-1 px-3 py-1 text-sm pl-6">Opening Balance</div>
              <div class="w-32 px-3 py-1 text-sm text-right border-l border-gray-200">{{ formatAmount(openingBalance) }}</div>
            </div>
            <div class="flex border-b border-gray-300">
              <div class="flex-1 px-3 py-1 text-sm pl-6">Current Period</div>
              <div class="w-32 px-3 py-1 text-sm text-right border-l border-gray-200">{{ formatAmount(currentPeriodPL) }}</div>
            </div>

            <!-- Difference in Opening Balance -->
            <div class="flex border-b border-gray-300">
              <div class="flex-1 px-3 py-1 text-sm">Difference in opening balances</div>
              <div class="w-32 px-3 py-1 text-sm text-right border-l border-gray-200">{{ formatAmount(differenceInOpening) }}</div>
            </div>
          </div>
        </div>

        <!-- Right Side - Assets -->
        <div class="w-1/2 flex flex-col">
          <!-- Column Header -->
          <div class="flex border-b border-black bg-gray-100">
            <div class="flex-1 px-3 py-2 font-bold text-black">Assets</div>
            <div class="w-32 px-3 py-2 text-right font-bold border-l border-black">Amount</div>
          </div>

          <!-- Assets Content -->
          <div class="flex-1">
            <!-- Fixed Assets -->
            <div class="px-3 py-1 font-bold text-sm border-b border-gray-300 bg-gray-50">
              Fixed Assets
            </div>
            <template v-if="o_balanceSheetData?.length > 0">
              <div
                v-for="(item, index) in o_balanceSheetData"
                :key="`o-${index}`"
                class="flex border-b border-gray-200"
              >
                <div class="flex-1 px-3 py-1 text-sm pl-6">{{ item?.ACType1Details }}</div>
                <div class="w-32 px-3 py-1 text-sm text-right border-l border-gray-200">{{ formatAmount(item?.Amount) }}</div>
              </div>
            </template>
            <div class="flex border-b border-gray-300">
              <div class="flex-1 px-3 py-1 text-sm font-semibold"></div>
              <div class="w-32 px-3 py-1 text-sm text-right font-semibold border-l border-gray-200">{{ formatAmount(o_total) }}</div>
            </div>

            <!-- Current Assets -->
            <div class="px-3 py-1 font-bold text-sm border-b border-gray-300 bg-gray-50">
              Current Assets
            </div>
            <template v-if="b_balanceSheetData?.length > 0">
              <div
                v-for="(item, index) in b_balanceSheetData"
                :key="`b-${index}`"
                class="flex border-b border-gray-200"
              >
                <div class="flex-1 px-3 py-1 text-sm pl-6">{{ item?.ACType1Details }}</div>
                <div class="w-32 px-3 py-1 text-sm text-right border-l border-gray-200">{{ formatAmount(item?.Amount) }}</div>
              </div>
            </template>
            <div class="flex border-b border-gray-300">
              <div class="flex-1 px-3 py-1 text-sm font-semibold"></div>
              <div class="w-32 px-3 py-1 text-sm text-right font-semibold border-l border-gray-200">{{ formatAmount(b_total) }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Footer Totals - Always aligned at bottom -->
      <div class="flex border-t-2 border-black bg-gray-100">
        <!-- Total Liabilities -->
        <div class="w-1/2 flex border-r border-black">
          <div class="flex-1 px-3 py-2 font-bold text-sm">Total</div>
          <div class="w-32 px-3 py-2 font-bold text-sm text-right border-l border-black">{{ formatAmount(totalLiabilities) }}</div>
        </div>
        <!-- Total Assets -->
        <div class="w-1/2 flex">
          <div class="flex-1 px-3 py-2 font-bold text-sm">Total</div>
          <div class="w-32 px-3 py-2 font-bold text-sm text-right border-l border-black">{{ formatAmount(totalAssets) }}</div>
        </div>
      </div>
    </div>

    <!-- Initial State (before any search) -->
    <div v-else class="flex flex-col items-center justify-center py-20 bg-white min-h-[calc(100vh-200px)]">
      <Icon
        icon="tabler:report-analytics"
        class="text-6xl text-gray-300 mb-4"
      />
      <p class="text-gray-500">
        Select a date range and click Preview to generate the balance sheet
      </p>
    </div>
  </MainLayout>
</template>

<script setup>
import MainLayout from "@/components/layouts/mainLayout.vue";
import { onMounted, ref, computed } from "vue";
import axios from "axios";
import { getToken } from "@/utilities/common.js";
import { apiBase } from "@/config.js";
import dayjs from "dayjs";
import * as XLSX from "xlsx";
import printJS from "print-js";
import { Icon } from "@iconify/vue";
import { message } from "ant-design-vue";

const SiteCode = "01";
const pdfLoading = ref(false);
const excelLoading = ref(false);

const category = ref("JVR");
const companyName = ref("Petra Products");

// Initialize as strings in YYYY-MM-DD so native <input type="date"> shows correct values
const DateFrom = ref(dayjs().startOf("month").format("YYYY-MM-DD"));
const DateTo = ref(dayjs().endOf("month").format("YYYY-MM-DD"));

const loading = ref(false);
const b_balanceSheetData = ref([]);
const b_total = ref(0);
const d_balanceSheetData = ref([]);
const d_total = ref(0);
const e_balanceSheetData = ref([]);
const e_total = ref(0);
const f_balanceSheetData = ref([]);
const f_total = ref(0);
const o_balanceSheetData = ref([]);
const o_total = ref(0);

// Profit & Loss related refs
const openingBalance = ref(0);
const currentPeriodPL = ref(0);
const differenceInOpening = ref(0);

// New ref to track if a search has been performed
const hasSearched = ref(false);

// Computed property to check if dates are valid
const dateError = computed(() => {
  if (!DateFrom.value || !DateTo.value) return false;
  return dayjs(DateTo.value).isBefore(dayjs(DateFrom.value));
});

// Computed property to check if there's data to display
const hasData = computed(() => {
  return (
    b_balanceSheetData.value?.length > 0 ||
    d_balanceSheetData.value?.length > 0 ||
    e_balanceSheetData.value?.length > 0 ||
    f_balanceSheetData.value?.length > 0 ||
    o_balanceSheetData.value?.length > 0
  );
});

// Computed totals
const totalLiabilities = computed(() => {
  return f_total.value + e_total.value + d_total.value + openingBalance.value + currentPeriodPL.value + differenceInOpening.value;
});

const totalAssets = computed(() => {
  return d_total.value + o_total.value + b_total.value;
});

// Helpers to format dates for display like `1st November 2025`
const getOrdinal = (d) => {
  const j = d % 10,
    k = d % 100;
  if (k >= 11 && k <= 13) return "th";
  if (j === 1) return "st";
  if (j === 2) return "nd";
  if (j === 3) return "rd";
  return "th";
};

const formatDisplayDate = (date) => {
  if (!date) return "";
  const d = dayjs(date);
  if (!d.isValid()) return date;
  const dayNum = d.date();
  const ord = getOrdinal(dayNum);
  return `${dayNum}${ord} ${d.format("MMMM YYYY")}`;
};

const formatRange = (from, to) => {
  return `${formatDisplayDate(from)} to ${formatDisplayDate(to)}`;
};

// Helper to format numbers with thousand separators
const formatAmount = (amount) => {
  if (amount == null || amount === "") return "0.00";
  const num = Number(amount);
  if (isNaN(num)) return "0.00";
  return num.toLocaleString("en-US", {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2,
  });
};

// Enhanced fetchBalanceSheet function
const fetchBalanceSheet = async () => {
  try {
    loading.value = true;
    hasSearched.value = true; // Mark that a search has been performed

    const res = await axios.post(
      `${apiBase}/journal-master/balance-sheet-new-report`,
      {
        DateFrom: dayjs(DateFrom.value).format("YYYY-MM-DD"),
        DateTo: dayjs(DateTo.value).format("YYYY-MM-DD"),
      },
      getToken()
    );

    if (res.data) {
      // Check if data is an array and handle accordingly
      b_balanceSheetData.value = Array.isArray(res?.data?.B?.data)
        ? res?.data?.B?.data
        : [];
      b_total.value = res?.data?.B?.total || 0;
      d_balanceSheetData.value = Array.isArray(res?.data?.D?.data)
        ? res?.data?.D?.data
        : [];
      d_total.value = res?.data?.D?.total || 0;
      e_balanceSheetData.value = Array.isArray(res?.data?.E?.data)
        ? res?.data?.E?.data
        : [];
      e_total.value = res?.data?.E?.total || 0;
      f_balanceSheetData.value = Array.isArray(res?.data?.F?.data)
        ? res?.data?.F?.data
        : [];
      f_total.value = res?.data?.F?.total || 0;
      f_balanceSheetData.value = Array.isArray(res?.data?.F?.data)
        ? res?.data?.F?.data
        : [];
      f_total.value = res?.data?.F?.total || 0;
      o_balanceSheetData.value = Array.isArray(res?.data?.O?.data)
        ? res?.data?.O?.data
        : [];
      o_total.value = res?.data?.O?.total || 0;

      // Show message if no data found
      if (b_balanceSheetData.value.length === 0) {
        message.info("No balance sheet entries found for the selected period");
      }
    } else {
      // If response structure is unexpected
      b_balanceSheetData.value = [];
      b_total.value = 0;
      // message.warning("Unexpected response format from server");
    }

    console.log("Balance sheet data:", b_balanceSheetData.value);
  } catch (err) {
    console.error("Error fetching balance sheet:", err);
    // Set empty data on error
    b_balanceSheetData.value = [];
    b_total.value = 0;
    hasSearched.value = true;

    // Show error message based on error type
    if (err.response?.status === 404) {
      message.error("Balance sheet data not found");
    } else if (err.response?.status === 500) {
      message.error("Server error. Please try again later");
    } else if (err.message === "Network Error") {
      message.error("Network error. Please check your connection");
    } else {
      message.error("Failed to fetch balance sheet data");
    }
  } finally {
    loading.value = false;
  }
};

const exportExcel = () => {
  if (!b_balanceSheetData.value.length) {
    message.warning("No data to export");
    return;
  }
  excelLoading.value = true;
  try {
    const wsData = [
      ["P-ERP PRODUCTS"],
      ["House No.# 90, Main Road, Nolvog, Nishat Nagar, Turag, Dhaka-1230"],
      ["Management Statement of Financial Position"],
      [`Period: ${formatRange(DateFrom.value, DateTo.value)}`],
      [],
      ["Particulars", formatDisplayDate(DateTo.value)],
      [],
      ["ASSETS", ""],
      [],
      ["Non Current Assets", "-"],
      [],
      ["Current Assets", formatAmount(Math.round(b_total.value))],
    ];

    // Add Current Assets details
    b_balanceSheetData.value.forEach((item) => {
      wsData.push([
        item.ACType1Details || "",
        formatAmount(Math.round(item.Amount || 0)),
      ]);
    });

    wsData.push(
      [],
      ["TOTAL ASSETS", formatAmount(Math.round(b_total.value))],
      [],
      ["EQUITIES & LIABILITIES", ""],
      [],
      ["Owners' Equity", formatAmount(f_total.value)]
    );

    // Add Owners' Equity details
    if (f_balanceSheetData.value && f_balanceSheetData.value.length > 0) {
      f_balanceSheetData.value.forEach((item) => {
        wsData.push([
          item.ACType1Details || "",
          formatAmount(item.Amount || 0),
        ]);
      });
    }

    wsData.push([], ["Non-Current Liabilities", formatAmount(e_total.value)]);

    // Add Non-Current Liabilities details
    if (e_balanceSheetData.value && e_balanceSheetData.value.length > 0) {
      e_balanceSheetData.value.forEach((item) => {
        wsData.push([
          item.ACType1Details || "",
          formatAmount(item.Amount || 0),
        ]);
      });
    }

    wsData.push([], ["Current Liabilities", formatAmount(d_total.value)]);

    // Add Current Liabilities details
    if (d_balanceSheetData.value && d_balanceSheetData.value.length > 0) {
      d_balanceSheetData.value.forEach((item) => {
        wsData.push([
          item.ACType1Details || "",
          formatAmount(item.Amount || 0),
        ]);
      });
    }

    wsData.push(
      [],
      [
        "TOTAL EQUITIES & LIABILITIES",
        formatAmount(f_total.value + e_total.value + d_total.value),
      ]
    );

    const ws = XLSX.utils.aoa_to_sheet(wsData);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Balance Sheet");
    XLSX.writeFile(wb, `Balance_Sheet_${dayjs().format("YYYY-MM-DD")}.xlsx`);
    // message.success("Excel file exported successfully");
  } catch (err) {
    console.error("Error exporting Excel:", err);
    message.error("Failed to export Excel file");
  } finally {
    excelLoading.value = false;
  }
};

const exportPDF = () => {
  if (!b_balanceSheetData.value || b_balanceSheetData.value.length === 0) {
    message.warning("No data to export");
    return;
  }

  pdfLoading.value = true;

  // Create a hidden printable HTML structure that matches the UI exactly
  const printContent = `
    <div style="width: 100%; max-width: 800px; margin: 0 auto; padding: 30px; font-family: Arial, sans-serif;">
      <!-- Header - matching UI exactly -->
      <div style="text-align: center; margin-bottom: 24px;">
        <h1 style="font-size: 24px; font-weight: bold; margin: 0 0 8px 0;">P-ERP PRODUCTS</h1>
        <p style="font-size: 12px; color: #666; margin: 0 0 4px 0;">
          House No.# 90, Main Road, Nolvog, Nishat Nagar, Turag, Dhaka-1230
        </p>
        <h2 style="font-size: 18px; font-weight: 600; margin: 0 0 4px 0;">
          Management Statement of Financial Position
        </h2>
        <p style="font-size: 12px; color: #666; margin: 0;">${formatRange(
          DateFrom.value,
          DateTo.value
        )}</p>
      </div>

      <!-- Header Row - matching UI's separate header boxes -->
      <div style="display: table; width: 100%; margin-bottom: 0;">
        <div style="display: table-cell; border: 1px solid black; padding: 8px 16px; font-weight: bold; vertical-align: middle;">
          Particulars
        </div>
        <div style="display: table-cell; border: 1px solid black; padding: 8px 16px; font-weight: bold; text-align: center; width: 33.33%; vertical-align: middle;">
          ${formatDisplayDate(DateTo.value)}
        </div>
      </div>

      <!-- Balance Sheet Table - matching UI layout -->
      <table style="width: 100%; border-collapse: collapse; font-size: 12px;">
        <tbody>
          <!-- ASSETS Section -->
          <tr>
            <td style="padding: 8px 16px; font-weight: bold; text-decoration: underline;">ASSETS</td>
            <td style="width: 33.33%;"></td>
          </tr>

          <!-- Non Current Assets -->
          <tr>
            <td style="padding: 4px 16px; font-weight: 600; text-decoration: underline;">
              Non Current Assets
            </td>
            <td style="text-align: right; padding: 4px 16px; font-weight: bold; width: 33.33%;">-</td>
          </tr>
          <tr><td colspan="2">&nbsp;</td></tr>

          <!-- Current Assets -->
          <tr>
            <td style="padding: 4px 16px; padding-top: 12px; font-weight: 600; text-decoration: underline;">
              Current Assets
            </td>
            <td style="text-align: right; padding: 4px 16px; padding-top: 12px; font-weight: bold; width: 33.33%;">
              ${formatAmount(Math.round(b_total.value))}
            </td>
          </tr>
          ${b_balanceSheetData.value
            .map(
              (item) => `
            <tr>
              <td style="padding: 4px 16px;">${item?.ACType1Details || ""}</td>
              <td style="text-align: right; padding: 4px 16px; width: 33.33%;">
                ${formatAmount(Math.round(item?.Amount || 0))}
              </td>
            </tr>
          `
            )
            .join("")}

          <!-- Total Assets -->
          <tr style="font-weight: bold;">
            <td style="padding: 8px 16px;">TOTAL ASSETS</td>
            <td style="text-align: right; padding: 8px 16px; border-bottom: 2px solid black; width: 33.33%;">
              ${formatAmount(Math.round(b_total.value))}
            </td>
          </tr>

          <!-- EQUITIES & LIABILITIES Section -->
          <tr>
            <td style="padding: 8px 16px; padding-top: 16px; font-weight: bold; text-decoration: underline;">
              EQUITIES & LIABILITIES
            </td>
            <td style="text-align: right; padding: 4px 16px; padding-top: 12px; font-weight: bold; width: 33.33%;"></td>
          </tr>

          <!-- Owners' Equity -->
          <tr>
            <td style="padding: 4px 16px; font-weight: 600; text-decoration: underline;">Owners' Equity</td>
            <td style="text-align: right; padding: 4px 16px; font-weight: bold; border-bottom: 2px solid black; width: 33.33%;">
              ${formatAmount(f_total.value)}
            </td>
          </tr>
          ${
            f_balanceSheetData.value && f_balanceSheetData.value.length > 0
              ? f_balanceSheetData.value
                  .map(
                    (item) => `
            <tr>
              <td style="padding: 4px 16px;">${item?.ACType1Details || ""}</td>
              <td style="text-align: right; padding: 4px 16px; width: 33.33%;">
                ${formatAmount(item?.Amount || 0)}
              </td>
            </tr>
          `
                  )
                  .join("")
              : ""
          }

          <!-- Non-Current Liabilities -->
          <tr>
            <td style="padding: 4px 16px; padding-top: 12px; font-weight: 600; text-decoration: underline;">
              Non-Current Liabilities
            </td>
            <td style="text-align: right; padding: 4px 16px; padding-top: 12px; font-weight: bold; border-bottom: 2px solid black; width: 33.33%;">
              ${formatAmount(e_total.value)}
            </td>
          </tr>
          ${
            e_balanceSheetData.value && e_balanceSheetData.value.length > 0
              ? e_balanceSheetData.value
                  .map(
                    (item) => `
            <tr>
              <td style="padding: 4px 16px;">${item?.ACType1Details || ""}</td>
              <td style="text-align: right; padding: 4px 16px; width: 33.33%;">
                ${formatAmount(item?.Amount || 0)}
              </td>
            </tr>
          `
                  )
                  .join("")
              : ""
          }

          <!-- Current Liabilities -->
          <tr>
            <td style="padding: 4px 16px; padding-top: 12px; font-weight: 600; text-decoration: underline;">
              Current Liabilities
            </td>
            <td style="text-align: right; padding: 4px 16px; padding-top: 12px; font-weight: bold; border-bottom: 2px solid black; width: 33.33%;">
              ${formatAmount(d_total.value)}
            </td>
          </tr>
          ${
            d_balanceSheetData.value && d_balanceSheetData.value.length > 0
              ? d_balanceSheetData.value
                  .map(
                    (item) => `
            <tr>
              <td style="padding: 4px 16px;">${item?.ACType1Details || ""}</td>
              <td style="text-align: right; padding: 4px 16px; width: 33.33%;">
                ${formatAmount(item?.Amount || 0)}
              </td>
            </tr>
          `
                  )
                  .join("")
              : ""
          }

          <!-- Total Equities & Liabilities -->
          <tr style="font-weight: bold;">
            <td style="padding: 8px 16px;">TOTAL EQUITIES & LIABILITIES</td>
            <td style="text-align: right; padding: 8px 16px; border-bottom: 3px double black; width: 33.33%;">
              ${formatAmount(f_total.value + e_total.value + d_total.value)}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  `;

  try {
    printJS({
      printable: printContent,
      type: "raw-html",
      style: `
        @page {
          size: A4;
          margin: 20mm;
        }
        
        * {
          box-sizing: border-box;
        }
        
        body {
          font-family: Arial, sans-serif;
          line-height: 1.5;
          color: #000;
          margin: 0;
          padding: 0;
        }
        
        table {
          page-break-inside: auto;
          width: 100% !important;
        }
        
        tr {
          page-break-inside: avoid;
          page-break-after: auto;
        }
        
        td {
          word-wrap: break-word;
          overflow-wrap: break-word;
        }

        h1, h2, p {
          margin: 0;
          padding: 0;
        }
      `,
      onLoadingStart: () => {
        pdfLoading.value = true;
      },
      onLoadingEnd: () => {
        pdfLoading.value = false;
      },
      onPrintDialogClose: () => {
        pdfLoading.value = false;
        // message.success("PDF print dialog opened successfully");
      },
      onError: (error) => {
        console.error("Print error:", error);
        pdfLoading.value = false;
        message.error("Failed to generate PDF");
      },
    });
  } catch (error) {
    console.error("PDF Export Error:", error);
    pdfLoading.value = false;
    message.error("Failed to export PDF");
  }
};
</script>

<style>
.ant-input-number-input {
  @apply !text-right !pr-10;
}

.overflow-x-auto::-webkit-scrollbar,
.overflow-y-auto::-webkit-scrollbar {
  display: none;
  width: 0;
  height: 0;
}

.overflow-x-auto,
.overflow-y-auto {
  scrollbar-width: none;
  -ms-overflow-style: none;
}

@media (min-width: 992px) and (max-width: 1400px) {
  .filter-grid.max-w-6xl {
    max-width: 62rem !important;
  }

  .print-view.max-w-6xl {
    max-width: 62rem !important;
  }

  .voucher-page-title {
    font-size: 14px !important;
  }
}

.report-table th,
.report-table td {
  font-size: 12px;
}
</style>
