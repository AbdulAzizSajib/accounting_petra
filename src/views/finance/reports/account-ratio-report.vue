<template>
  <MainLayout>
    <div class="flex items-center">
      <h1 class="text-2xl font-bold text-primary">Account Ratio Report</h1>
      <div class="p-8 flex items-end space-x-6">
        <div>
          <label for="startDate">From Date</label>

          <input
            type="date"
            :max="DateTo"
            v-model="DateFrom"
            class="w-full border p-1 border-black rounded-md"
          />
        </div>
        <div>
          <label for="endDate">To Date</label>
          <input
            type="date"
            :min="DateFrom"
            v-model="DateTo"
            class="w-full border p-1 border-black rounded-md"
          />
        </div>
        <a-button type="primary" class="" @click="fetchAllData"
          >Preview</a-button
        >
      </div>
    </div>

    <div v-if="loading" class="flex justify-center mt-4">
      <a-spin></a-spin>
    </div>

    <div class="" v-if="allData">
      <!-- report table -->
      <div
        id="trialBalanceToPrint"
        class="p-6 bg-white rounded-lg shadow-md max-w-6xl mx-auto border"
      >
        <!--  -->
        <!-- <div class="flex justify-end gap-2 pb-4">
          <button @click="exportExcel" class="" :disabled="excelLoading">
            <a-spin v-if="excelLoading" size="small" class="spinner-white" />
            <span>
              <Icon icon="vscode-icons:file-type-excel" class="text-4xl" />
            </span>
          </button>

          <button @click="exportPDF" :disabled="pdfLoading">
            <a-spin v-if="pdfLoading" size="small" class="spinner-white" />
            <span v-if="!pdfLoading">
              <Icon icon="vscode-icons:file-type-pdf2" class="text-4xl" />
            </span>
          </button>
        </div> -->
        <!-- header area start -->
        <div class="flex justify-center mb-4 items-center space-x-10 relative">
          <div class="text-center mb-6">
            <h1 class="text-2xl font-bold mb-2">PETRA PRODUCTS</h1>
            <p class="text-sm text-gray-600 mb-1">
              House No.# 90, Main Road, Nolvog, Nishat Nagar, Turag, Dhaka-1230
            </p>
            <h2 class="text-lg font-semibold mb-1">
              Management Statement of Financial Position
            </h2>
            <p class="text-sm text-gray-600">
              {{ formatRange(DateFrom, DateTo) }}
            </p>

            <div class="absolute top-0 right-0 flex items-center gap-5">
              <button @click="exportPDF" :loading="pdfLoading">
                <Icon
                  icon="vscode-icons:file-type-pdf2"
                  width="30"
                  height="30"
                />
              </button>

              <!-- <button @click="exportExcel" :loading="excelLoading">
                <Icon
                  icon="vscode-icons:file-type-excel"
                  width="30"
                  height="30"
                />
              </button> -->
            </div>
          </div>
        </div>
        <!-- table area -->
        <div class="overflow-x-auto mt-8">
          <table class="w-full border-collapse border border-black">
            <tr>
              <td class="border border-black px-4 py-2">
                <b>1. Liquidity Ratios</b>
              </td>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2"></td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Current ratio</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = Current Assets / Current Liabilities -->
                {{ formatNumber(allData?.Current_Ratio || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Quick ratio</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = (Current Assets - Inventory) / Current Liabilities -->
                {{ formatNumber(allData?.Quick_Ratio || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">
                Working capital turnover ratio
              </td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = Net Sales / Working Capital -->
                {{ formatNumber(allData?.Working_Capital || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2">
                <b>2. Solvency Ratios</b>
              </td>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2"></td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Debt-assets ratios</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = Total Debt / Total Assets -->
                {{ formatNumber(allData?.Debt_Asset_Ratio || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">
                Interest coverage ratios
              </td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = Net income / Interest Expense -->
                {{ formatNumber(allData?.Interest_Coverage_Ratio || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2">
                <b>3. Profitability Ratios</b>
              </td>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2"></td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Profit margin ratio</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = Net Income / Net Sales) x 100 -->
                {{ formatNumber(allData?.Profit_Margin || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Return on assets</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = Net Income / Average Total Assets -->
                {{ formatNumber(allData?.Return_On_Assets || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Gross margin ratio</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = (Total Revenue - Cost of Goods Sold) / Total Revenue -->
                {{ formatNumber(allData?.Gross_Margin || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Return on capital</td>
              <td class="border border-black px-4 py-2 text-right">
                {{ formatNumber(allData?.Return_On_Capital || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2">
                <b>4. Efficiency Ratios</b>
              </td>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2"></td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">
                Asset Turnover ratio
              </td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = Net Sales / Average Total Assets -->
                {{ formatNumber(allData?.Asset_Turnover_Ratio || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Inventory turnover</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = Cost of Goods Sold / Average Inventory -->
                {{ formatNumber(allData?.Inventory_Turnover || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">
                Day's sales in inventory
              </td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = ( COGS / Average inventory ) ×365 days -->
                {{ formatNumber(allData?.Days_Sales_In_Inventory || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Overhead Ratio</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = (total salary / total sales) x 100 -->
                {{ formatNumber(allData?.Overhead_Ratio || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">
                Marketing cost Ratio
              </td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = (Marketing cost / total sales) x 100 -->
                {{ formatNumber(allData?.Marketing_Cost_Ratio || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Damage Ratio</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = (Total Damage/Total sales) x 100 -->
                {{ formatNumber(allData?.Damage_Ratio || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Receivable turnover</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = total sales / Account Receivable -->
                {{ formatNumber(allData?.Receivable_Turnover || 0) }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">
                Average receivables collection day
              </td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = 365/Receivable turnover -->
                {{
                  formatNumber(allData?.Average_Receivables_Collection_Day || 0)
                }}
              </td>
            </tr>
            <tr>
              <td class="border border-black px-4 py-2"></td>
              <td class="border border-black px-4 py-2">Payables turnover</td>
              <td class="border border-black px-4 py-2 text-right">
                <!-- = (COGS + change in inventory ) or Purchase / Average accounts
                payable -->
                {{ formatNumber(allData?.Payables_Turnover || 0) }}
              </td>
            </tr>
          </table>
        </div>
      </div>
    </div>
  </MainLayout>
</template>

<script setup>
import MainLayout from "@/components/layouts/mainLayout.vue";
import { onMounted, ref, computed } from "vue";
import { Icon } from "@iconify/vue";
import axios from "axios";
import { apiBase, formatNumber } from "@/config";
import { getToken, showNotification } from "@/utilities/common";
import dayjs from "dayjs";
import * as XLSX from "xlsx";
import printJS from "print-js";

const selectedDate = ref(dayjs());

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

const formattedToday = computed(() => {
  return dayjs().format("DD-MMM-YYYY").toLowerCase();
});

const formattedTime = computed(() => {
  return dayjs().format("HH:mm:ss");
});

const formattedDate = computed(() => {
  return selectedDate.value.format("MMM, YYYY");
});

const DateFrom = ref(dayjs().startOf("month").format("YYYY-MM-DD"));
const DateTo = ref(dayjs().endOf("month").format("YYYY-MM-DD"));

const allData = ref();
const loading = ref(false);
const pdfLoading = ref(false);
const excelLoading = ref(false);

// Fetch allData with search and pagination
const fetchAllData = async () => {
  if (!DateFrom.value) {
    showNotification("warning", "Please select From Date.");
    return;
  }

  if (!DateTo.value) {
    showNotification("warning", "Please select To Date.");
    return;
  }
  loading.value = true;
  try {
    const res = await axios.post(
      `${apiBase}/journal-master/accounting_ratio`,
      {
        DateFrom: DateFrom.value,
        DateTo: DateTo.value,
      },
      getToken()
    );

    loading.value = false;
    allData.value = res?.data;
  } catch (err) {
    loading.value = false;
    console.error("Failed to fetch allData:", err);
    showNotification("error", "Failed to fetch trial balance data.");
  }
};

// Excel Export
const exportExcel = () => {
  if (!allData.value.length) return;
  excelLoading.value = true;
  try {
    const wsData = [
      ["SL", "Account Code", "Account Description", "Debit", "Credit"],
    ];

    allData.value.forEach((data, index) => {
      wsData.push([
        index + 1,
        data?.AMCode || "",
        data?.AMDetails || "",
        data?.Debit || "",
        data?.Credit || "",
      ]);
    });

    // Add totals row
    wsData.push(["", "", "Total:", totalDebit.value, totalCredit.value]);

    const ws = XLSX.utils.aoa_to_sheet(wsData);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Trial Balance");
    XLSX.writeFile(
      wb,
      `Trial_Balance_${selectedDate.value.format("YYYY-MM-DD")}.xlsx`
    );
    showNotification("success", "Excel file exported successfully!");
  } catch (err) {
    console.error("Error exporting Excel:", err);
    showNotification("error", "Failed to export Excel file.");
  } finally {
    excelLoading.value = false;
  }
};

const exportPDF = () => {
  pdfLoading.value = true;
  printJS({
    printable: "trialBalanceToPrint",
    type: "html",
    targetStyles: ["*"],
    style: `
      @page {
        size: A4 portrait;
        margin: 15mm 10mm;
      }

      @media print {
        * {
          -webkit-print-color-adjust: exact !important;
          print-color-adjust: exact !important;
          box-sizing: border-box !important;
        }

        body {
          font-family: Arial, sans-serif !important;
          margin: 0 !important;
          padding: 0 !important;
        }

        #trialBalanceToPrint {
          width: 100% !important;
          max-width: 100% !important;
          margin: 0 auto !important;
          padding: 10px !important;
          background: white !important;
          box-shadow: none !important;
          border: none !important;
        }

        /* Header layout - centered */
        #trialBalanceToPrint .flex.justify-center {
          display: flex !important;
          justify-content: center !important;
          align-items: center !important;
          margin-bottom: 15px !important;
          position: relative !important;
        }

        #trialBalanceToPrint .text-center.mb-6 {
          text-align: center !important;
        }

        #trialBalanceToPrint h1 {
          font-size: 18px !important;
          font-weight: bold !important;
          margin: 0 0 8px 0 !important;
        }

        #trialBalanceToPrint h2 {
          font-size: 14px !important;
          font-weight: 600 !important;
          margin: 0 0 6px 0 !important;
        }

        #trialBalanceToPrint p {
          font-size: 11px !important;
          margin: 3px 0 !important;
        }

        /* Hide the absolute positioned buttons in print */
        #trialBalanceToPrint .absolute.top-0.right-0 {
          display: none !important;
        }

        /* Table container */
        .overflow-x-auto {
          overflow: visible !important;
          margin-top: 15px !important;
        }

        /* Main ratio table styling */
        #trialBalanceToPrint .overflow-x-auto table {
          width: 100% !important;
          border-collapse: collapse !important;
          border: 1px solid #000 !important;
          margin: 0 !important;
        }

        #trialBalanceToPrint .overflow-x-auto table tr {
          page-break-inside: avoid !important;
          break-inside: avoid !important;
        }

        #trialBalanceToPrint .overflow-x-auto table td {
          font-size: 10px !important;
          padding: 6px 8px !important;
          border: 1px solid #000 !important;
          vertical-align: middle !important;
        }

        /* Column widths for ratio table */
        #trialBalanceToPrint .overflow-x-auto table td:nth-child(1) {
          width: 25% !important;
          text-align: left !important;
        }

        #trialBalanceToPrint .overflow-x-auto table td:nth-child(2) {
          width: 45% !important;
          text-align: left !important;
        }

        #trialBalanceToPrint .overflow-x-auto table td:nth-child(3) {
          width: 30% !important;
          text-align: right !important;
        }

        /* Bold text styling */
        #trialBalanceToPrint .overflow-x-auto table td b {
          font-weight: bold !important;
        }

        /* Force borders on all sides of each cell */
        #trialBalanceToPrint .overflow-x-auto table tr td:first-child {
          border-left: 1px solid #000 !important;
        }

        #trialBalanceToPrint .overflow-x-auto table tr td:last-child {
          border-right: 1px solid #000 !important;
        }

        #trialBalanceToPrint .overflow-x-auto table tr:first-child td {
          border-top: 1px solid #000 !important;
        }

        #trialBalanceToPrint .overflow-x-auto table tr:last-child td {
          border-bottom: 1px solid #000 !important;
        }

        /* Hide non-printable elements */
        button,
        .ant-button,
        .flex.justify-end.gap-2,
        .ant-spin,
        h1.text-primary,
        .p-8.flex.items-end,
        .flex.items-center.gap-5 {
          display: none !important;
        }

        /* Remove hover effects */
        tr:hover {
          background-color: transparent !important;
        }
      }
    `,
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

<style scoped>
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
</style>
