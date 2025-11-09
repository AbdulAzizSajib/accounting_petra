<template>
  <MainLayout>
    <h1 class="text-2xl font-bold text-primary mb-4">Search Voucher</h1>
    <!-- Filter -->
    <div class="grid lg:grid-cols-6 gap-4 items-end">
      <div class="">
        <label class="mb-1 font-semibold block text-sm"
          >Code
          <LoadingOutlined class="ml-1 align-baseline" v-if="code_loading"
        /></label>
        <a-select
          class="w-full !text-sm"
          placeholder="Select Voucher Type"
          showSearch
          allowClear
          :filterOption="false"
          v-model:value="formData.Type"
          @input="debounceGetCode($event?.target?.value)"
          @select="getCategory(formData.Type)"
        >
          <template v-for="(data, index) in codeData" :key="index">
            <a-select-option :value="data?.JVType"
              >{{ data?.JVType }} - {{ data?.JVDetails }}
            </a-select-option>
          </template>
        </a-select>
      </div>
      <div>
        <label class="mb-1 font-semibold block text-sm"
          >Category
          <LoadingOutlined class="ml-1 align-baseline" v-if="category_loading"
        /></label>
        <a-select
          class="w-full"
          placeholder="Select Voucher Type"
          v-model:value="formData.Category"
        >
          <template v-for="(data, index) in categoryData" :key="index">
            <a-select-option :value="data?.Short">{{
              data?.Category
            }}</a-select-option></template
          >
        </a-select>
      </div>
      <div>
        <label class="mb-1 font-semibold block text-sm">Vouchar No From</label>
        <a-input-number
          type="number"
          placeholder="Enter Voucher No"
          class="w-full"
          v-model:value="formData.VoucherFrom"
          @input="
            () => {
              if (formData.VoucherTo) {
                if (Number(formData.VoucherFrom) > Number(formData.VoucherTo)) {
                  showNotification('warning', 'Enter valid number');
                  formData.VoucherFrom = '';
                }
              }
            }
          "
        />
      </div>
      <div>
        <label class="mb-1 font-semibold block text-sm">Vouchar No To</label>
        <a-input-number
          type="number"
          placeholder="Enter Voucher No"
          class="w-full"
          v-model:value="formData.VoucherTo"
        />
      </div>

      <div>
        <label class="mb-1 font-semibold block text-sm">Period</label>
        <a-date-picker
          class="w-full"
          picker="month"
          placeholder="Select Date From"
          v-model:value="formData.Period"
        />
      </div>
      <div>
        <div class="flex gap-1">
          <a-button class="px-6 w-full" type="primary" @click="getReport()"
            >Preview</a-button
          >
          <a-button
            type="danger"
            class="px-6 w-full shadow-orange-50 bg-green-600 text-white"
            @click="$router.go(-1)"
            >Back</a-button
          >
        </div>
      </div>
    </div>
    <hr class="my-4" />
    <!-- table -->
    <a-button
      type="danger"
      class="px-6 shadow-orange-50 bg-green-600 text-white"
      >Print All</a-button
    >

    <hr class="my-4" />
    <div class="flex justify-end items-end">
      <a-button
        type="danger"
        class="px-6 shadow-orange-50 bg-green-600 text-white"
        >Print</a-button
      >
    </div>

    <div v-for="(data, index) in reportData" :key="index">
      <h2 class="font-normal text-base uppercase mb-1 text-center">
        Journal Voucher
      </h2>
      <h3 class="font-semibold text-base capitalize mb-3 text-center">
        Petra Food and Snacks
      </h3>

      <div>
        <h4 class="uppercase mb-0 font-normal">
          Voucher No: <span class="font-semibold">{{ data?.at(0)?.JvNo }}</span>
        </h4>

        <div class="overflow-x-auto">
          <table
            class="border border-gray-400 border-collapse w-full text-left"
          >
            <thead>
              <tr>
                <th
                  class="border border-gray-400 w-4 whitespace-nowrap px-3 py-1 text-sm font-normal"
                >
                  DATE:
                  <span class="font-semibold">{{ data?.at(0)?.Period }}</span>
                </th>
                <th class="border border-gray-400"></th>
                <th class="border border-gray-400"></th>
              </tr>
              <tr class="text-center">
                <th
                  class="uppercase w-2 border border-gray-400 px-3 py-1 text-sm font-normal"
                >
                  Account Code
                </th>
                <th
                  class="uppercase w-2 border border-gray-400 px-3 py-1 text-sm font-normal"
                >
                  Account Name
                </th>
                <th
                  class="uppercase w-2 border border-gray-400 px-3 py-1 text-sm font-normal"
                >
                  SUB Ledger
                </th>
                <th
                  class="uppercase w-2 border border-gray-400 px-3 py-1 text-sm font-normal"
                >
                  SUB Ledger Name
                </th>
                <th
                  class="uppercase w-2 border border-gray-400 px-3 py-1 text-sm font-normal"
                >
                  DEBIT-TAKA
                </th>
                <th
                  class="uppercase w-2 border border-gray-400 px-3 py-1 text-sm font-normal"
                >
                  CREDIT-TAKA
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-if="report_Loading">
                <td colspan="6"><a-skeleton /></td>
              </tr>
              <tr v-if="!report_Loading && !data?.length">
                <td colspan="6"><a-empty /></td>
              </tr>

              <tr v-for="item in data">
                <td class="capitalize border border-gray-400 px-3 py-1 text-sm">
                  {{ item?.AMCode || "-" }}
                </td>
                <td class="capitalize border border-gray-400 px-3 py-1 text-sm">
                  {{ item?.AMDetails || "-" }}
                </td>
                <td class="capitalize border border-gray-400 px-3 py-1 text-sm">
                  -
                </td>
                <td class="capitalize border border-gray-400 px-3 py-1 text-sm">
                  -
                </td>
                <td
                  class="capitalize border border-gray-400 px-3 py-1 text-sm text-right"
                >
                  {{ formatNumber(item?.Debit || 0) }}
                </td>
                <td
                  class="capitalize border border-gray-400 px-3 py-1 text-sm text-right"
                >
                  {{ formatNumber(item?.Credit || 0) }}
                </td>
              </tr>
            </tbody>
            <tfoot>
              <tr class="">
                <td
                  class="capitalize border border-t-2 border-gray-400 border-t-gray-600 px-3 py-1 text-sm"
                ></td>
                <td
                  colspan="3"
                  class="capitalize border border-t-2 border-gray-400 border-t-gray-600 px-3 py-1 text-sm text-right"
                >
                  TOTAL ===>
                </td>
                <td
                  class="capitalize border border-t-2 border-gray-400 border-t-gray-600 px-3 py-1 text-sm text-right font-semibold"
                >
                  {{ calculateTotal(data, "Debit") }}
                </td>
                <td
                  class="capitalize border border-t-2 border-gray-400 border-t-gray-600 px-3 py-1 text-sm text-right font-semibold"
                >
                  {{ calculateTotal(data, "Credit") }}
                </td>
              </tr>
              <tr>
                <td
                  colspan="6"
                  class="capitalize border border-gray-400 px-3 py-1 text-sm font-semibold"
                >
                  {{ numberToTakaWords(calculateTotal(data, "Debit")) }}
                </td>
              </tr>
              <tr>
                <td
                  colspan="6"
                  class="border border-gray-400 px-3 py-1 text-xs"
                >
                  test of narration
                </td>
              </tr>
            </tfoot>
          </table>
        </div>
        <div class="overflow-x-auto">
          <table class="w-full">
            <tr>
              <td class="text-sm">
                Prepared <br />
                By
              </td>
              <td class="text-sm">
                Audited <br />
                By
              </td>
              <td class="text-sm">
                Authorised <br />
                By
              </td>
            </tr>
            <tr>
              <td colspan="3" class="border-b-2 border-b-gray-600 pt-10"></td>
            </tr>
            <tr>
              <td class="text-sm">Posted By:</td>
            </tr>
          </table>
        </div>
      </div>
    </div>
  </MainLayout>
</template>

<script setup>
import MainLayout from "@/components/layouts/mainLayout.vue";
import axios from "axios";
import { ref } from "vue";
import { apiBase } from "@/config";
import { getToken, showNotification } from "@/utilities/common";
import { onMounted } from "vue";
import { debounce } from "lodash";
import { LoadingOutlined } from "@ant-design/icons-vue";
import dayjs from "dayjs";

function formatNumber(num) {
  if (isNaN(num)) return num;
  return Number(num).toLocaleString("en-US", {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2,
  });
}

const codeData = ref([]);
const categoryData = ref([]);
const code_loading = ref(false);
const category_loading = ref(false);
const report_Loading = ref(false);

const getCode = async (search = "") => {
  code_loading.value = true;
  try {
    const res = await axios.get(
      `${apiBase}/settings/voucher-type/all?search=${search}`,
      getToken()
    );
    code_loading.value = false;

    if (res?.data?.success == true) {
      codeData.value = res?.data?.data;
    }
  } catch (err) {
    code_loading.value = false;
    codeData.value = [];
    showNotification("error", err?.response?.data?.message || err?.message);
  }
};
const getCategory = async (code = "") => {
  category_loading.value = true;
  try {
    const res = await axios.get(
      `${apiBase}/settings/voucher-type-category/show?JVType=${code}`,
      getToken()
    );
    category_loading.value = false;

    if (res?.data?.success == true) {
      categoryData.value = res?.data?.data;
    }
  } catch (err) {
    category_loading.value = false;
    categoryData.value = [];
    showNotification("error", err?.response?.data?.message || err?.message);
  }
};
const debounceGetCode = debounce(getCode, 300);
onMounted(async () => {
  await getCode();
});

// Table Data
const formData = ref({
  SiteCode: "01",
  Period: "",
  Type: "",
  Category: "",
  VoucherFrom: "",
  VoucherTo: "",
});
const reportData = ref([]);

const getReport = async () => {
  const date = new Date(formData.value.Period);
  const formattedPeriod = `${date.getFullYear()}${String(
    date.getMonth() + 1
  ).padStart(2, "0")}`;

  const data = {
    ...formData.value,
    Period: formattedPeriod,
  };
  report_Loading.value = true;
  try {
    const res = await axios.post(
      `${apiBase}/journal-master/report`,
      data,
      getToken()
    );
    report_Loading.value = false;
    reportData.value = res?.data;
  } catch (err) {
    report_Loading.value = false;
    reportData.value = [];
    showNotification("error", err?.response?.data?.message || err?.message);
  }
};

const calculateTotal = (tableData, field) => {
  if (!Array.isArray(tableData)) return "0.00";
  return tableData
    .reduce((sum, item) => sum + (parseFloat(item[field]) || 0), 0)
    .toFixed(2);
};
function numberToTakaWords(num) {
  num = parseInt(num);
  if (isNaN(num)) return "";
  const a = [
    "",
    "One",
    "Two",
    "Three",
    "Four",
    "Five",
    "Six",
    "Seven",
    "Eight",
    "Nine",
    "Ten",
    "Eleven",
    "Twelve",
    "Thirteen",
    "Fourteen",
    "Fifteen",
    "Sixteen",
    "Seventeen",
    "Eighteen",
    "Nineteen",
  ];
  const b = [
    "",
    "",
    "Twenty",
    "Thirty",
    "Forty",
    "Fifty",
    "Sixty",
    "Seventy",
    "Eighty",
    "Ninety",
  ];

  function inWords(n) {
    if ((n = n.toString()).length > 9) return "Overflow";
    let nStr = ("000000000" + n).substr(-9);
    let crore = parseInt(nStr.substr(0, 2));
    let lakh = parseInt(nStr.substr(2, 2));
    let thousand = parseInt(nStr.substr(4, 2));
    let hundred = parseInt(nStr.substr(6, 1));
    let tens = parseInt(nStr.substr(7, 2));
    let str = "";

    if (crore) str += (a[crore] || b[crore]) + " Crore ";
    if (lakh) str += (a[lakh] || b[lakh]) + " Lakh ";
    if (thousand) str += (a[thousand] || b[thousand]) + " Thousand ";
    if (hundred) str += a[hundred] + " Hundred ";
    if (tens) {
      if (tens < 20) str += a[tens] + " ";
      else str += b[Math.floor(tens / 10)] + " " + a[tens % 10] + " ";
    }
    return str.trim();
  }

  return `Taka ${inWords(num)} Only`;
}
</script>

<style>
.ant-input-number-input {
  @apply !text-right !pr-10;
}
</style>
