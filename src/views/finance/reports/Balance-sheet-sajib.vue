<template>
  <MainLayout>
    <div class="grid grid-cols-12 gap-4 items-center mb-4">
      <div class="col-span-2">
        <div class="mb-2">
          <div class="flex gap-2 items-center relative">
            <p class="text-2xl voucher-page-title font-bold text-primary">
              Balance Sheet Report
            </p>
          </div>
        </div>
      </div>
      <div class="col-span-8">
        <div
          class="grid filter-grid m-auto grid-cols-12 border border-gray-200 rounded-md py-4 px-3 gap-2 mb-2"
        >
          <div class="col-span-4">
            <div class="flex items-center">
              <label for="period" class="w-36 font-bold">Form Date:</label>
              <div class="w-full">
                <input
                  type="date"
                  v-model="DateFrom"
                  :max="DateTo"
                  class="w-full border p-1 border-black rounded-md"
                  :class="{ 'border-red-500': dateError }"
                />
              </div>
            </div>
          </div>
          <div class="col-span-4">
            <div class="flex items-center">
              <label for="period" class="w-36 font-bold">To Date:</label>
              <div class="w-full">
                <input
                  type="date"
                  v-model="DateTo"
                  :min="DateFrom"
                  class="w-full border p-1 border-black rounded-md"
                  :class="{ 'border-red-500': dateError }"
                />
                <p v-if="dateError" class="text-red-500 text-xs mt-1">
                  To Date must be greater than or equal to From Date
                </p>
              </div>
            </div>
          </div>
          <div class="col-span-1">
            <div>
              <a-button
                type="primary"
                @click="fetchBalanceSheet"
                :loading="loading"
                :disabled="dateError"
                >Preview</a-button
              >
            </div>
          </div>
          <div class="col-span-1">
            <div>
              <a-button
                type="primary"
                @click="fetchBalanceSheet"
                :loading="loading"
                :disabled="dateError"
                >PDF</a-button
              >
            </div>
          </div>
          <div class="col-span-1">
            <div>
              <a-button
                type="primary"
                @click="fetchBalanceSheet"
                :loading="loading"
                :disabled="dateError"
                >Excel</a-button
              >
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Loading State -->
    <div v-if="loading" class="flex justify-center items-center py-20">
      <a-spin size="large" />
    </div>

    <!-- No Data Found State (after search) -->
    <div
      v-else-if="
        hasSearched && (!b_balanceSheetData || b_balanceSheetData.length === 0)
      "
      class="flex flex-col items-center justify-center py-20"
    >
      <Icon icon="tabler:file-off" class="text-6xl text-gray-400 mb-4" />
      <h2 class="text-xl font-semibold text-gray-600 mb-2">No Data Found</h2>
      <p class="text-gray-500 text-sm">
        No balance sheet data available for the period
        {{ formatRange(DateFrom, DateTo) }}
      </p>
      <!-- <a-button type="primary" @click="resetFilters" class="mt-4">
        Reset Filters
      </a-button> -->
    </div>

    <!-- Balance Sheet Data Display -->
    <div
      v-else-if="b_balanceSheetData && b_balanceSheetData.length > 0"
      class="w-full max-w-4xl mx-auto p-8 border"
    >
      <!-- Header -->
      <div class="text-center mb-6">
        <h1 class="text-2xl font-bold mb-2">PETRA PRODUCTS</h1>
        <p class="text-sm text-gray-600 mb-1">
          House No.# 90, Main Road, Nolvog, Nishat Nagar, Turag, Dhaka-1230
        </p>
        <h2 class="text-lg font-semibold mb-1">
          Management Statement of Financial Position
        </h2>
        <p class="text-sm text-gray-600">{{ formatRange(DateFrom, DateTo) }}</p>
      </div>

      <!-- Balance Sheet Table -->
      <div class="flex w-full justify-between gap-6">
        <div class="font-bold px-4 py-2 border border-black w-full">
          Particulars
        </div>
        <div class="font-bold px-4 py-2 border border-black w-1/3">
          {{ formatDisplayDate(DateTo) }}
        </div>
      </div>
      <div class="bg-white">
        <table class="w-full">
          <thead>
            <tr class=" "></tr>
          </thead>
          <tbody>
            <!-- ASSETS Section -->
            <tr>
              <td class="px-4 py-2 font-bold underline">ASSETS</td>
              <td></td>
            </tr>

            <!-- Non Current Assets -->
            <tr>
              <td class="px-4 py-1 font-semibold underline">
                Non Current Assets
              </td>
              <td class="text-right px-4 py-1 font-bold">-</td>
            </tr>
            <tr></tr>

            <!-- Current Assets -->
            <tr>
              <td class="px-4 py-1 pt-3 font-semibold underline">
                Current Assets
              </td>
              <td class="text-right px-4 py-1 pt-3 font-bold">
                {{ formatAmount(Math.round(b_total)) }}
              </td>
            </tr>
            <template v-if="b_balanceSheetData?.length > 0">
              <tr
                v-for="(value, index) in b_balanceSheetData"
                :key="`a-${index}`"
              >
                <td class="px-4 py-1">{{ value?.ACType1Details }}</td>
                <td class="text-right px-4 py-1">
                  {{ formatAmount(Math.round(value?.Amount)) }}
                </td>
              </tr>
            </template>

            <!-- Total Assets -->
            <tr class="font-bold">
              <td class="px-4 py-2">TOTAL ASSETS</td>
              <td class="text-right px-4 py-2 border-b-2 border-black">
                {{ formatAmount(Math.round(b_total)) }}
              </td>
            </tr>

            <!-- EQUITIES & LIABILITIES Section -->
            <tr>
              <td class="px-4 py-2 pt-4 font-bold underline">
                EQUITIES & LIABILITIES
              </td>
              <td class="text-right px-4 py-1 pt-3 font-bold"></td>
            </tr>

            <!-- Owners' Equity -->
            <tr>
              <td class="px-4 py-1 font-semibold underline">Owners' Equity</td>
              <td
                class="text-right px-4 py-1 font-bold border-b-2 border-black"
              >
                {{ formatAmount(f_total) }}
              </td>
            </tr>
            <template v-if="f_balanceSheetData?.length > 0">
              <tr
                v-for="(value, index) in f_balanceSheetData"
                :key="`a-${index}`"
              >
                <td class="px-4 py-1">{{ value?.ACType1Details }}</td>
                <td class="text-right px-4 py-1">
                  {{ formatAmount(value?.Amount) }}
                </td>
              </tr>
            </template>

            <!-- Non-Current Liabilities -->
            <tr>
              <td class="px-4 py-1 pt-3 font-semibold underline">
                Non-Current Liabilities
              </td>
              <td
                class="text-right px-4 py-1 pt-3 font-bold border-b-2 border-black"
              >
                {{ formatAmount(e_total) }}
              </td>
            </tr>
            <template v-if="e_balanceSheetData?.length > 0">
              <tr
                v-for="(value, index) in e_balanceSheetData"
                :key="`a-${index}`"
              >
                <td class="px-4 py-1">{{ value?.ACType1Details }}</td>
                <td class="text-right px-4 py-1">
                  {{ formatAmount(value?.Amount) }}
                </td>
              </tr>
            </template>

            <!-- Current Liabilities -->
            <tr>
              <td class="px-4 py-1 pt-3 font-semibold underline">
                Current Liabilities
              </td>
              <td
                class="text-right px-4 py-1 pt-3 font-bold border-b-2 border-black"
              >
                {{ formatAmount(d_total) }}
              </td>
            </tr>
            <template v-if="d_balanceSheetData?.length > 0">
              <tr
                v-for="(value, index) in d_balanceSheetData"
                :key="`a-${index}`"
              >
                <td class="px-4 py-1">{{ value?.ACType1Details }}</td>
                <td class="text-right px-4 py-1">
                  {{ formatAmount(value?.Amount) }}
                </td>
              </tr>
            </template>

            <!-- Total Equities & Liabilities -->
            <tr class="font-bold">
              <td class="px-4 py-2">TOTAL EQUITIES & LIABILITIES</td>
              <td
                class="text-right px-4 py-2 border-b-2 border-double border-black"
              >
                {{ formatAmount(f_total + e_total + d_total) }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Initial State (before any search) -->
    <div v-else class="flex flex-col items-center justify-center py-20">
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

// New ref to track if a search has been performed
const hasSearched = ref(false);

// Computed property to check if dates are valid
const dateError = computed(() => {
  if (!DateFrom.value || !DateTo.value) return false;
  return dayjs(DateTo.value).isBefore(dayjs(DateFrom.value));
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
      `${apiBase}/journal-master/balance-sheet-report`,
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

// Reset function to clear filters and data
const resetFilters = () => {
  DateFrom.value = dayjs().startOf("month").format("YYYY-MM-DD");
  DateTo.value = dayjs().endOf("month").format("YYYY-MM-DD");
  b_balanceSheetData.value = [];
  b_total.value = 0;
  hasSearched.value = false;
  message.success("Filters have been reset");
};

const exportExcel = () => {
  if (!b_balanceSheetData.value.length) {
    message.warning("No data to export");
    return;
  }
  excelLoading.value = true;
  try {
    const wsData = [
      ["PETRA PRODUCTS"],
      ["Management Statement of Financial Position"],
      [`Period: ${formatRange(DateFrom.value, DateTo.value)}`],
      [],
      ["Particulars", "Amount"],
      ["ASSETS", ""],
      ["Current Assets", formatAmount(b_total.value)],
    ];

    b_balanceSheetData.value.forEach((item) => {
      wsData.push([item.FundName || "", formatAmount(item.Amount || 0)]);
    });

    wsData.push(
      ["TOTAL ASSETS", formatAmount(b_total.value)],
      [],
      ["EQUITIES & LIABILITIES", ""],
      ["TOTAL EQUITIES & LIABILITIES", formatAmount(b_total.value)]
    );

    const ws = XLSX.utils.aoa_to_sheet(wsData);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Balance Sheet");
    XLSX.writeFile(wb, `Balance_Sheet_${dayjs().format("YYYY-MM-DD")}.xlsx`);
    message.success("Excel file exported successfully");
  } catch (err) {
    console.error("Error exporting Excel:", err);
    message.error("Failed to export Excel file");
  } finally {
    excelLoading.value = false;
  }
};

const exportPDF = () => {
  if (!b_balanceSheetData.value.length) {
    message.warning("No data to export");
    return;
  }

  printJS({
    printable: "balanceSheetToPrint",
    type: "html",
    targetStyles: ["*"],
    style: `
      @page {
        size: A4;
        margin: 10mm;
      }
      
      @media print {
        body {
          font-family: Arial, sans-serif !important;
          font-size: 10px !important;
          line-height: 1.2 !important;
          margin: 0 !important;
          padding: 0 !important;
          -webkit-print-color-adjust: exact !important;
          color-adjust: exact !important;
        }
        
        #balanceSheetToPrint {
          width: 100% !important;
          margin: 0 !important;
          padding: 0 !important;
          background: white !important;
        }
        
        .table-print th,
        .table-print td {
          border: 1px solid #000 !important;
          padding: 4px 3px !important;
          font-size: 9px !important;
        }
        
        button,
        .flex.justify-end,
        .ant-spin,
        .ant-btn {
          display: none !important;
        }
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
      message.success("PDF exported successfully");
    },
  });
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
