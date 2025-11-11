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
              <input
                type="date"
                v-model="DateFrom"
                class="w-full border p-1 border-black rounded-md"
              />
            </div>
          </div>
          <div class="col-span-4">
            <div class="flex items-center">
              <label for="period" class="w-36 font-bold">To Date:</label>
              <input
                type="date"
                v-model="DateTo"
                class="w-full border p-1 border-black rounded-md"
              />
            </div>
          </div>
          <div class="col-span-1">
            <div>
              <a-button
                type="primary"
                @click="fetchBalanceSheet"
                :loading="loading"
                >Preview</a-button
              >
            </div>
          </div>
        </div>
      </div>
    </div>

    <div
      v-if="b_balanceSheetData?.length > 0"
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
        <!-- <p class="text-sm text-gray-600">1st July 2023  to 30 June 2024</p> -->
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
            <tr class=" ">
              <!-- <th class="text-left py-2 px-4 font-semibold border">
                Particulars
              </th>
              <th class="text-right py-2 px-4 font-semibold border">
                30 June 2024
              </th> -->
            </tr>
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
            <tr>
              <!-- <td class="px-4 py-1">Property, Plant & Equipment</td>
              <td class="text-right px-4 py-1 border-b border-black">
                20,025,507
              </td> -->
            </tr>

            <!-- Current Assets -->
            <tr>
              <td class="px-4 py-1 pt-3 font-semibold underline">
                Current Assets
              </td>
              <td class="text-right px-4 py-1 pt-3 font-bold">
                {{ formatAmount(b_total) }}
              </td>
            </tr>
            <template v-if="b_balanceSheetData?.length > 0">
              <tr
                v-for="(value, index) in b_balanceSheetData"
                :key="`a-${index}`"
              >
                <td class="px-4 py-1">{{ value?.FundName }}</td>
                <td class="text-right px-4 py-1">
                  {{ formatAmount(value?.Amount) }}
                </td>
              </tr>
            </template>

            <!-- Total Assets -->

            <tr class="font-bold">
              <td class="px-4 py-2">TOTAL ASSETS</td>
              <td class="text-right px-4 py-2 border-b-2 border-black">
                {{ formatAmount(b_total) }}
              </td>
            </tr>

            <!-- EQUITIES & LIABILITIES Section -->
            <tr>
              <td class="px-4 py-2 pt-4 font-bold underline">
                EQUITIES & LIABILITIES
              </td>
              <td></td>
            </tr>

            <!-- Owners' Equity -->
            <tr>
              <td class="px-4 py-1 font-semibold underline">Owners' Equity</td>
              <td class="text-right px-4 py-1 font-bold">-</td>
            </tr>
            <!-- <tr>
              <td class="px-4 py-1">Retained Earnings</td>
              <td class="text-right px-4 py-1 border-b border-black">
                171,189,454
              </td>
            </tr> -->

            <!-- Non-Current Liabilities -->
            <tr>
              <td class="px-4 py-1 pt-3 font-semibold underline">
                Non-Current Liabilities
              </td>
              <td class="text-right px-4 py-1 pt-3 font-bold">-</td>
            </tr>
            <!-- <tr>
              <td class="px-4 py-1">AB Bank Loan Liabilities</td>
              <td class="text-right px-4 py-1 border-b border-black">
                44,248,549
              </td>
            </tr> -->

            <!-- Current Liabilities -->
            <tr>
              <td class="px-4 py-1 pt-3 font-semibold underline">
                Current Liabilities
              </td>
              <td class="text-right px-4 py-1 pt-3 font-bold">-</td>
            </tr>
            <!-- <tr>
              <td class="px-4 py-1">Short Term Loan</td>
              <td class="text-right px-4 py-1">12,719,504</td>
            </tr> -->

            <!-- Total Equities & Liabilities -->
            <tr class="font-bold">
              <td class="px-4 py-2">TOTAL EQUITIES & LIABILITIES</td>
              <td
                class="text-right px-4 py-2 border-b-2 border-double border-black"
              >
                {{ formatAmount(b_total) }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
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

const SiteCode = "01";
const pdfLoading = ref(false);
const excelLoading = ref(false);

const category = ref("JVR");
// Initialize as strings in YYYY-MM-DD so native <input type="date"> shows correct values
// (HTML date inputs expect a value in the format YYYY-MM-DD)
const DateFrom = ref(dayjs().startOf("month").format("YYYY-MM-DD"));
const DateTo = ref(dayjs().endOf("month").format("YYYY-MM-DD"));

const loading = ref(false);
const b_balanceSheetData = ref([]);
const b_total = ref(0);

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

const fetchBalanceSheet = async () => {
  try {
    loading.value = true;

    const res = await axios.post(
      `${apiBase}/journal-master/balance-sheet-report`,
      {
        DateFrom: dayjs(DateFrom.value).format("YYYY-MM-DD"),
        DateTo: dayjs(DateTo.value).format("YYYY-MM-DD"),
      },
      getToken()
    );
    b_balanceSheetData.value = res.data.B.data;
    b_total.value = res.data.B.total;
    // console.log(b_balanceSheetData.value);
    loading.value = false;
  } catch (err) {
    console.error("Error fetching vouchers:", err);
    loading.value = false;
  }
};

const exportExcel = () => {
  if (!voucherData.value.length) return;
  excelLoading.value = true;
  try {
    const wsData = [
      [
        "Period",
        "Vou No",
        "Date",
        "M. Code",
        "M. Accounts Description",
        "Debit",
        "Credit",
        "Narration",
      ],
    ];
    voucherData.value.forEach((voucherGroup) => {
      voucherGroup.forEach((item) => {
        wsData.push([
          item.Period || "",
          item.JvNo || "",
          item.JVdate ? dayjs(item.JVdate).format("DD-MMM-YYYY") : "",
          item.AMCode || "",
          item.AMDetails || "",
          Number(item.Debit || 0),
          Number(item.Credit || 0),
          item.Narration || "",
        ]);
      });
    });
    const ws = XLSX.utils.aoa_to_sheet(wsData);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Journal Book");
    XLSX.writeFile(wb, `Journal_Book_${dayjs().format("YYYY-MM-DD")}.xlsx`);
  } catch (err) {
    console.error("Error exporting Excel:", err);
  } finally {
    excelLoading.value = false;
  }
};

const exportPDF = () => {
  printJS({
    printable: "journalBookToPrint",
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
        
        #journalBookToPrint {
          width: 100% !important;
          margin: 0 !important;
          padding: 0 !important;
          background: white !important;
        }
        
        #journalBookToPrint .flex.justify-between {
          display: flex !important;
          justify-content: space-between !important;
          align-items: flex-start !important;
          margin-bottom: 15px !important;
          width: 100% !important;
        }
       #journalBookToPrint .table-print th,
        .min-w-full th {
          border: 1px solid #000 !important;
          padding: 4px 3px !important;
          font-size: 9px !important;
          font-weight: bold !important;
          text-align: left !important;
          -webkit-print-color-adjust: exact !important;
        }
        #journalBookToPrint .table-print th,
        .min-w-full th.text-right{
        text-align: right !important;
        }

        #journalBookToPrint .text-left.mb-6 {
          flex: 1 !important;
          margin-bottom: 0 !important;
        }
        
        #journalBookToPrint .text-left.mb-6 h1 {
          font-size: 14px !important;
          font-weight: bold !important;
          color: #000 !important;
          margin: 0 0 3px 0 !important;
        }
        
        #journalBookToPrint .text-left.mb-6 p {
          font-size: 12px !important;
          font-weight: bold !important;
          color: #000 !important;
          margin: 0 !important;
        }
        
        #journalBookToPrint .flex.gap-10 {
          display: flex !important;
          gap: 15px !important;
          flex-shrink: 0 !important;
        }
        
        #journalBookToPrint .flex.gap-10 table {
          border: 1px solid #000 !important;
          border-collapse: collapse !important;
          width: 140px !important;
          font-size: 9px !important;
        }
        
        #journalBookToPrint .flex.gap-10 th {
          border: 1px solid #000 !important;
          padding: 3px 4px !important;
          font-weight: bold !important;
          text-align: left !important;
          -webkit-print-color-adjust: exact !important;
        }
        
        #journalBookToPrint .flex.gap-10 td {
          border: 1px solid #000 !important;
          padding: 2px 4px !important;
          font-size: 8px !important;
        }
        
        .table-print,
        .min-w-full {
          width: 100% !important;
          border: 1px solid #000 !important;
          border-collapse: collapse !important;
          margin-top: 10px !important;
          font-size: 9px !important;
        }
        
        .table-print th,
        .min-w-full th {
          border: 1px solid #000 !important;
          padding: 4px 3px !important;
          font-size: 9px !important;
          font-weight: bold !important;
          text-align: center !important;
          -webkit-print-color-adjust: exact !important;
        }
        
        .table-print td,
        .min-w-full td {
          border: 1px solid #000 !important;
          padding: 3px !important;
          font-size: 8px !important;
          vertical-align: top !important;
          word-wrap: break-word !important;
        }
        
        .table-print td.font-bold,
        .min-w-full td.font-bold {
          font-weight: bold !important;
          -webkit-print-color-adjust: exact !important;
        }
        
        .bg-gray-100 {
          -webkit-print-color-adjust: exact !important;
        }
        
        .bg-gray-100 td {
          font-weight: bold !important;
        }
        
        .text-right {
          text-align: right !important;
        }
        
        .overflow-x-auto {
          overflow: visible !important;
        }
        
        button,
        .flex.justify-end,
        .ant-spin,
        .ant-btn,
        .ant-select,
        .ant-picker,
        .p-4,
        .grid {
          display: none !important;
        }
        
        .table-print {
          page-break-inside: auto !important;
        }
        
        .table-print tr {
          page-break-inside: avoid !important;
        }
        
        .table-print thead {
          display: table-header-group !important;
        }
      }
    `,
    css: null,
    scanStyles: false,
    onLoadingStart: () => {
      pdfLoading.value = true;
    },
    onLoadingEnd: () => {
      pdfLoading.value = false;
    },
    onPrintDialogClose: () => {
      pdfLoading.value = false;
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
