<template>
  <MainLayout>
    <div class="">
      <div class="my-1">
        <a-form>
          <!-- form top -->
          <div class="p-3 shadow rounded bg-gray-100">
            <div class="grid md:grid-cols-12 gap-4">
              <div class="col-span-2">
                <!-- Date and Voucher Type -->
                <div class="flex items-center">
                  <label for="date" class="w-28">Date: </label>
                  <a-date-picker v-model:value="form.date" format="DD/MM/YYYY" class="w-full" ref="date_ref"
                    @keydown.enter.stop="onDateEnter" />
                </div>
              </div>
              <!--type -->
              <div class="col-span-4">
                <div class="flex items-center">
                  <label for="voucher-type" class="w-36">Voucher Type:</label>
                  <a-select v-model:value="form.voucherType" ref="voucher_type_ref" class="w-full"
                    placeholder="Select Voucher Type" @select="category_ref?.focus()" disabled>
                    <a-select-option v-for="item in voucherTypes" :key="item.JVType" :value="item.JVType">
                      {{ item.JVType }} - {{ item.JVDetails }} -
                      {{ item.Location }} - {{ item.AMCode }}
                    </a-select-option>
                  </a-select>
                </div>
              </div>
              <!--  -->
              <div class="col-span-6">
                <a-input disabled :value="selectedVoucherDetails" class="w-full bg-[#006dc1] text-white" />
              </div>
              <!-- category -->
              <div class="col-span-2">
                <div class="flex items-center">
                  <label for="voucher-type" class="w-28">Category:</label>

                  <a-select v-model:value="form.category" ref="category_ref" class="w-full"
                    placeholder="Select Category" @select="group_code_ref?.focus()" disabled>
                    <a-select-option v-for="cat in categories" :key="cat.Short" :value="cat.Short">
                      {{ cat.Category }}
                    </a-select-option>
                  </a-select>
                </div>
              </div>
              <!-- voucher number -->
              <div class="col-span-2">
                <div class="flex items-center">
                  <label for="voucher-number" class="w-56">Voucher: </label>
                  <a-input v-model:value="form.voucherNumber" class="w-full text-center bg-[#006dc1] text-white"
                    disabled />
                </div>
              </div>
              <!-- sub ledger -->
              <div class="col-span-2">
                <div class="flex items-center">
                  <label for="sub-ledger" class="w-36">Sub-Ledger:</label>
                  <a-input class="w-full bg-[#006dc1] text-white" disabled />
                </div>
              </div>
            </div>
          </div>

          <div class="space-y-2">
            <div class="grid grid-cols-2 gap-8 mt-4">
              <!-- Group -->
              <div>
                <div class="flex items-center">
                  <label for="account-code" class="w-[130px]">Group:</label>

                  <a-select allow-clear v-model:value="form.group" placeholder="" ref="group_code_ref"
                    class="cursor-pointer w-full" @select="handleGroupSelect">
                    <a-select-option v-for="cat in group" :key="cat.GroupCode" :value="cat.GroupCode">
                      {{ cat.GroupDetails }}
                    </a-select-option>
                  </a-select>
                </div>
              </div>

              <!-- Type -->
              <div class="flex items-center">
                <label for="account-code" class="w-16">Type: </label>

                <a-select allow-clear v-model:value="form.type" class="w-full text-white" ref="type_ref"
                  @select="account_head_ref?.focus()">
                  <a-select-option v-for="cat in Type" :key="cat.ACType1" :value="cat.ACType1">
                    {{ cat.ACType1Details }}
                  </a-select-option>
                </a-select>
              </div>
            </div>
            <!--Account Head -->
            <div class="flex items-center">
              <label for="account-head" class="w-[117px]">Account Head:</label>

              <a-select allow-clear v-model:value="form.account_head" placeholder="" class="w-full"
                ref="account_head_ref" @select="sub_ledger_ref?.focus()">
                <a-select-option v-for="cat in account_head" :key="cat.AMCode" :value="cat.AMCode">
                  {{ cat.AMDetails }}
                </a-select-option>
              </a-select>
            </div>
            <div class="grid grid-cols-1 lg:grid-cols-2 items-center gap-x-4 mt-2">
              <div class="flex items-center">
                <label class="w-36">Sub-Ledger:</label>

                <a-select allow-clear v-model:value="form.subLedger" placeholder="" class="w-full" ref="sub_ledger_ref"
                  @select="person_ref?.focus()">
                  <a-select-option v-for="cat in subLedgerList" :key="cat.ASCode" :value="cat.ASCode">
                    {{ cat.ASDetails }}
                  </a-select-option>
                </a-select>
              </div>
              <div class="flex items-center">
                <label class="w-36">Person:</label>
                <a-input v-model:value="form.person" ref="person_ref" @keydown.enter="cheque_no_ref?.focus()" />
              </div>
            </div>
            <div class="grid grid-cols-1 lg:grid-cols-2 items-center gap-x-4 mt-2">
              <div class="flex items-center">
                <label class="w-36">Cheque No:</label>

                <a-select show-search allowClear :filter-option="false" v-model:value="form.chequeNo"
                  placeholder="Select Cheque No" class="w-full" ref="cheque_no_ref" @select="vendor_id_ref?.focus()"
                  @search="(event) => {
                    chequeRegisterList = all_chequeRegisterList.filter((item) =>
                      item?.ChequeNo?.toLowerCase().includes(event.toLowerCase()) ||
                      item?.ChequeBookNo?.toLowerCase().includes(event.toLowerCase())
                    );
                  }">
                  <a-select-option v-for="cheque in chequeRegisterList" :key="cheque.ChequeNo" :value="cheque.ChequeNo">
                    {{ cheque.ChequeNo }}
                  </a-select-option>
                </a-select>
              </div>
              <!-- Vendor Select -->
              <div class="flex items-center">
                <label class="w-36">Vendor ID:</label>
                <a-select v-model:value="form.vendorId" show-search placeholder="Select Vendor" class="w-full"
                  allow-clear ref="vendor_id_ref" @keydown.enter="cheque_name_ref?.focus()">
                  <a-select-option v-for="vendor in vendorList" :key="vendor.VendorId" :value="vendor.VendorId">
                    {{ vendor.VendorId }} - {{ vendor.VendorName }}
                  </a-select-option>
                </a-select>
              </div>
            </div>
            <div class="flex items-center">
              <label class="w-36">Cheque Name:</label>
              <a-input v-model:value="form.chequeName" ref="cheque_name_ref" @keydown.enter="bill_no_ref?.focus()" />
            </div>
            <div class="grid grid-cols-1 lg:grid-cols-2 items-center gap-x-4 mt-2">
              <div class="flex items-center">
                <label class="w-36">Bill No</label>

                <a-input v-model:value="form.billNo" ref="bill_no_ref" @keydown.enter="bill_date_ref?.focus()" />
              </div>
              <div class="flex items-center">
                <label class="w-36">Bill Date</label>
                <a-date-picker v-model:value="form.billDate" format="DD/MM/YYYY" class="w-full" ref="bill_date_ref"
                  @keydown.enter.stop="onBillDateEnter" />
              </div>
            </div>

            <div class="flex items-center mt-2">
              <label class="w-[117px]">Narration</label>

              <a-input v-model:value="form.narration" ref="narration_ref" @keydown.enter="debit_ref?.focus()" />
            </div>

            <div class="flex mt-2">
              <div class="flex items-center">
                <label class="w-[155px]">Debit:</label>

                <a-input-number v-model:value="form.debit" class="w-full" ref="debit_ref"
                  @focus="lastFocusedField = 'debit'" @keydown.enter="credit_ref?.focus()" />
              </div>
              <div class="flex items-center ms-8">
                <label class="w-24">Credit:</label>
                <a-input-number v-model:value="form.credit" class="w-full" ref="credit_ref"
                  @focus="lastFocusedField = 'credit'" @keydown.enter="add_button_ref?.$el?.focus()" />
              </div>
            </div>
          </div>

          <div class="mt-4 flex items-center space-x-4">
            <a-button type="primary" class="px-6 bg-primary text-white" @click="updateVoucher" :loading="creating"
              :disabled="!canUpdateVoucher">update</a-button>
            <a-button type="primary" class="px-6 bg-blue-500 text-white" @click="addEntry" ref="add_button_ref"
              :disabled="!canAddEntry">Add</a-button>
            <label class="flex items-center space-x-2">
              <a-checkbox v-model:checked="isContra" />
              <span>Contra</span>
            </label>
          </div>
        </a-form>
      </div>

      <!-- insert voucher end-->
    </div>
    <div class="shadow rounded-lg p-4 border border-gray-300 my-4">
      <!-- title -->
      <h1 class="text-2xl font-bold text-primary mb-2">
        Voucher ({{ voucherEntries.length }})
      </h1>

      <table class="w-full text-left">
        <thead>
          <tr class="bg-primary text-white">
            <th class="border border-white px-4 py-2">Account Code</th>
            <th class="border border-white px-4 py-2">Account Details</th>
            <th class="border border-white px-4 py-2">Sub Ledger</th>
            <th class="border border-white px-4 py-2 text-center">Debit</th>
            <th class="border border-white px-4 py-2 text-center">Credit</th>
            <th class="border border-white px-4 py-2">Cheque No</th>
            <th class="border border-white px-4 py-2">Cheque Name</th>
            <th class="border border-white px-4 py-2">Narration</th>
            <th class="border border-white px-4 py-2 text-center">Actions</th>
          </tr>
        </thead>
        <tbody class="capitalize">
          <tr v-for="(entry, index) in voucherEntries" :key="index" class="bg-gray-100">
            <!-- Account Code -->
            <td class="px-4 border">
              <a-input v-if="entry.isEditing" v-model:value="entry.account_head" class="w-full" />
              <span v-else>{{ entry.account_head }}</span>
            </td>

            <!-- Account Details -->
            <td class="px-4 border">
              <a-input v-if="entry.isEditing" v-model:value="entry.accountDetails" class="w-full" />
              <span v-else>{{ entry.accountDetails }}</span>
            </td>

            <!-- Sub Ledger -->
            <td class="px-4 border">
              <a-input v-if="entry.isEditing" v-model:value="entry.subLedger" class="w-full" />
              <span v-else>{{ entry.subLedger }}</span>
            </td>

            <!-- Debit -->
            <td class="px-4 border text-right w-40">
              <a-input-number v-if="entry.isEditing" v-model:value="entry.debit" class="w-full" />
              <span v-else>{{ entry.debit }}</span>
            </td>

            <!-- Credit -->
            <td class="px-4 border text-right w-40">
              <a-input-number v-if="entry.isEditing" v-model:value="entry.credit" class="w-full" />
              <span v-else>{{ entry.credit }}</span>
            </td>

            <!-- Cheque No -->
            <td class="px-4 border w-40">
              <a-input v-if="entry.isEditing" v-model:value="entry.chequeNo" class="w-full" />
              <span v-else>{{ entry.chequeNo }}</span>
            </td>

            <!-- Cheque Name -->
            <td class="px-4 border">
              <a-input v-if="entry.isEditing" v-model:value="entry.chequeName" class="w-full" />
              <span v-else>{{ entry.chequeName }}</span>
            </td>

            <!-- Narration -->
            <td class="px-4 border">
              <a-input v-if="entry.isEditing" v-model:value="entry.narration" class="w-full" />
              <span v-else>{{ entry.narration }}</span>
            </td>

            <!-- Actions -->
            <td class="px-4 border text-center w-8">
              <div class="flex justify-center gap-x-3">
                <!--                <button-->
                <!--                  v-if="entry.isEditing"-->
                <!--                  @click="saveEntryEdit(entry)"-->
                <!--                  class="px-2 py-1 bg-green-600 text-white rounded-md"-->
                <!--                >-->
                <!--                  Save-->
                <!--                </button>-->
                <button @click="populateFormFromEntry(entry, index)" class="px-2 py-1 bg-primary text-white rounded-md">
                  Edit
                </button>
                <!--                <button-->
                <!--                  @click="deleteEntry(index)"-->
                <!--                  class="px-2 py-1 bg-red-600 text-white rounded-md"-->
                <!--                >-->
                <!--                  Delete-->
                <!--                </button>-->
              </div>
            </td>
          </tr>
        </tbody>
        <tr class="mt-8 justify-center">
          <td colspan="2"></td>
          <td class="font-bold py-4">Total:</td>
          <td>
            <div class="w-full h-8 bg-teal-500 text-white text-center flex justify-center items-center rounded">
              {{ debitTotal.toFixed(2) }}
            </div>
          </td>
          <td>
            <div class="w-full h-8 bg-blue-700 text-white text-center flex justify-center items-center rounded">
              {{ creditTotal.toFixed(2) }}
            </div>
          </td>
          <!--          <td>-->
          <!--            <span-->
          <!--              class="w-full h-8 bg-blue-700 text-white text-center flex justify-center items-center rounded"-->
          <!--            >-->
          <!--              {{ contraTotal.toFixed(2) }}-->
          <!--            </span>-->
          <!--          </td>-->
        </tr>
      </table>
    </div>
  </MainLayout>
</template>

<script setup>
import MainLayout from "@/components/layouts/mainLayout.vue";
import {ref, onMounted, computed, watch, onUnmounted} from "vue";
import dayjs from "dayjs";
import axios from "axios";
import { apiBase } from "@/config.js";
import { getToken, showNotification } from "@/utilities/common.js";
import { Icon } from "@iconify/vue";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const router = useRouter();

// Get the route parameters
const period = ref(route.params.Period);
const siteCode = ref(route.params.SiteCode);
const jvNo = ref(route.params.JVNo);
const isContra = ref(false);

console.log(period.value, siteCode.value, jvNo.value);

const goBack = () => {
  router.push({ name: "temp-voucher" });
};

// Form data
const form = ref({
  date: dayjs(),
  voucherType: null,
  category: "",
  account_head: "",
  type: "",
  accountDetails: "",
  voucherNumber: 0,
  group: "",
  person: "",
  subLedger: null,
  narration: null,
  billNo: null,
  billDate: dayjs(),
  debit: 0,
  credit: 0,
  chequeNo: null,
  chequeName: null,
});

// State refs
const voucherEntries = ref([]);
const voucherTypes = ref([]);
const categories = ref([]);
const group = ref([]);
const Type = ref([]);
const account_head = ref([]);
const subLedgerList = ref([]);
const chequeRegisterList = ref([]);
const creating = ref(false);
const voucher_idwise_data = ref();
const all_chequeRegisterList = ref([]);

// Refs for focus management
const date_ref = ref();
const voucher_type_ref = ref();
const category_ref = ref();
const group_code_ref = ref();
const type_ref = ref();
const account_head_ref = ref();
const sub_ledger_ref = ref();
const person_ref = ref();
const cheque_no_ref = ref();
const cheque_name_ref = ref();
const bill_no_ref = ref();
const bill_date_ref = ref();
const narration_ref = ref();
const debit_ref = ref();
const credit_ref = ref();
const add_button_ref = ref();
const vendor_id_ref = ref();

// Computed properties
const canAddEntry = computed(() => {
  const f = form.value;
  return (
    f.voucherType &&
    f.account_head &&
    f.category &&
    (f.debit > 0 || f.credit > 0)
  );
});

const canUpdateVoucher = computed(() => voucherEntries.value.length > 0);

const selectedVoucher = computed(() =>
  voucherTypes.value.find((v) => v.JVType === form.value.voucherType)
);

const selectedVoucherDetails = computed(() => {
  if (!selectedVoucher.value) return "";
  const { JVDetails, Address1, Address2, AMCode } = selectedVoucher.value;
  return `${JVDetails || ""} - ${Address1 || ""} - ${Address2 || ""} - ${
    AMCode || ""
  }`;
});

const debitTotal = computed(() =>
  voucherEntries.value.reduce((sum, e) => sum + (Number(e.debit) || 0), 0)
);

const creditTotal = computed(() =>
  voucherEntries.value.reduce((sum, e) => sum + (Number(e.credit) || 0), 0)
);

const contraTotal = computed(() => debitTotal.value - creditTotal.value);

// Fetch Functions
const fetchVoucherTypes = async () => {
  try {
    const { data } = await axios.get(`${apiBase}/voucher/type`, getToken());
    voucherTypes.value = data;
  } catch (err) {
    console.error("Error fetching voucher types:", err);
    showNotification("error", "Failed to load voucher types");
  }
};

const fetchCategories = async (jvType) => {
  if (!jvType) {
    categories.value = [];
    return;
  }
  try {
    const { data } = await axios.get(
      `${apiBase}/voucher-type/category?JVType=${jvType || " "}`,
      getToken()
    );
    if (Array.isArray(data)) {
      categories.value = data;
    }
  } catch (err) {
    console.error("Error fetching categories:", err);
    categories.value = [];
  }
};

const fetchgroup = async () => {
  try {
    const { data } = await axios.get(`${apiBase}/journal/group`, getToken());
    if (Array.isArray(data)) {
      group.value = data;
    }
  } catch (err) {
    console.error("Error fetching groups:", err);
    group.value = [];
  }
};

const fetchType = async (groupCode) => {
  try {
    const { data } = await axios.get(
      `${apiBase}/journal/type?GroupCode=${groupCode}`,
      getToken()
    );
    if (Array.isArray(data)) {
      Type.value = data;
    }
  } catch (err) {
    console.error("Error fetching types:", err);
    Type.value = [];
  }
};

const fetchAccount_head = async () => {
  try {
    const { data } = await axios.get(
      `${apiBase}/journal/account-head`,
      getToken()
    );
    if (Array.isArray(data)) {
      account_head.value = data;
    }
  } catch (err) {
    console.error("Error fetching account heads:", err);
    account_head.value = [];
  }
};

const fetchSubLedgerList = async () => {
  try {
    const res = await axios.get(`${apiBase}/settings/ac-sub/all`, getToken());
    if (res.data.success) {
      subLedgerList.value = res.data.data;
    }
  } catch (err) {
    console.error("Error fetching sub-ledgers:", err);
  }
};

// Fetch and populate voucher data
const voucher_idwise = async () => {
  try {
    const res = await axios.get(
      `${apiBase}/journal-master/show?Period=${period.value}&JVNo=${jvNo.value}&SiteCode=${siteCode.value}`,
      getToken()
    );
    if (res.data) {
      voucher_idwise_data.value = res.data;
      // Populate form after data is loaded
      populateFormFromResponse();
    }
  } catch (error) {
    console.error("Error fetching voucher:", error);
    showNotification("error", "Failed to load voucher data");
  }
};

// Populate form from API response
const populateFormFromResponse = () => {
  if (!voucher_idwise_data.value) return;

  const data = voucher_idwise_data.value;

  // Populate master form fields
  form.value.date = data.JVDate ? dayjs(data.JVDate) : dayjs();
  form.value.voucherType = data.JVType;
  form.value.category = data.JVCat;
  form.value.voucherNumber = data.JVSerial || data.JVNo;

  // Populate voucher entries from details
  if (Array.isArray(data.details) && data.details.length > 0) {
    voucherEntries.value = data.details.map((detail) => {
      const accountDetails = getAccountDetails(detail.AMCode);

      return {
        account_head: detail.AMCode,
        accountDetails: accountDetails,
        type: detail.ASCode,
        subLedger: detail.ASCode,
        person: detail.Person || "",
        chequeNo: detail.ChequeNo || "",
        chequeName: detail.ChequeName || "",
        narration: detail.Narration || "",
        debit: parseFloat(detail.Debit) || 0,
        credit: parseFloat(detail.Credit) || 0,
        billNo: detail.BillNo || "",
        billDate: detail.BillDate ? dayjs(detail.BillDate) : dayjs(),
        group: "",
        voucherType: data.JVType,
        category: data.JVCat,
        date: data.JVDate ? dayjs(data.JVDate) : dayjs(),
        isEditing: false,
        RecNo: detail.RecNo,
        AMCode: detail.AMCode,
        ASCode: detail.ASCode,
      };
    });
  }
};

// Helper Functions
const getAccountDetails = (code) => {
  const found = account_head.value.find((c) => c.AMCode === code);
  return found ? found.AMDetails : "";
};

const handleGroupSelect = (value) => {
  type_ref.value?.focus();
  fetchType(value);
};

const onDateEnter = (e) => {
  const picker = date_ref.value;
  if (!picker.open) {
    e.preventDefault();
    voucher_type_ref.value?.focus();
  }
};

const onBillDateEnter = (e) => {
  const picker = bill_date_ref.value;
  if (!picker.open) {
    e.preventDefault();
    narration_ref.value?.focus();
  }
};

const lastFocusedField = ref(null);
// Entry Management
// const addEntry = () => {
//   const accountDetails = getAccountDetails(form.value.account_head);
//
//   voucherEntries.value.push({
//     ...form.value,
//     accountDetails,
//     isEditing: false,
//   });
//
//   form.value.subLedger = null;
//   form.value.type = null;
//   form.value.account_head = null;
//   form.value.person = "";
//   form.value.narration = null;
//   form.value.billNo = null;
//   form.value.billDate = dayjs();
//   form.value.debit = 0;
//   form.value.credit = 0;
//   form.value.chequeNo = null;
//   form.value.chequeName = null;
//   form.value.accountDetails = "";
//
//   account_head_ref.value?.focus();
// };

const populateFormFromEntry = (entry, index) => {
  form.value.account_head = entry.account_head;
  form.value.subLedger = entry.subLedger;
  form.value.type = entry.type;
  form.value.person = entry.person || "";
  form.value.chequeNo = entry.chequeNo || null;
  form.value.chequeName = entry.chequeName || null;
  form.value.billNo = entry.billNo || null;
  form.value.billDate = entry.billDate ? dayjs(entry.billDate) : dayjs();
  form.value.narration = entry.narration || null;
  form.value.debit = entry.debit || 0;
  form.value.credit = entry.credit || 0;
  form.value.accountDetails = entry.accountDetails || "";

  // Remove the entry from the table
  voucherEntries.value.splice(index, 1);

  // Optional: Focus on the first editable field after populating
  setTimeout(() => {
    account_head_ref.value?.focus();
  }, 100);

  // Optional: Show a notification
  // showNotification("info", "Entry loaded into form for editing");
};

const addEntry = () => {
  const f = form.value;

  const lastInput = lastFocusedField.value;

  if (lastInput === "debit") {
    f.credit = 0;
  } else if (lastInput === "credit") {
    f.debit = 0;
  }

  const entry = {
    ...JSON.parse(JSON.stringify(f)),
    accountDetails: getAccountDetails(f.account_head),
    isEditing: false,
  };

  voucherEntries.value.push(entry);

  f.subLedger = null;
  f.type = null;
  f.account_head = null;
  f.person = "";
  f.narration = null;
  f.billNo = null;
  f.billDate = dayjs();
  f.debit = 0;
  f.credit = 0;
  f.chequeNo = null;
  f.chequeName = null;
  f.accountDetails = "";

  account_head_ref.value?.focus();
};

const saveEntryEdit = (entry) => {
  entry.isEditing = false;
  showNotification("success", "Entry updated successfully");
};

const deleteEntry = (index) => {
  voucherEntries.value.splice(index, 1);
  showNotification("success", "Entry deleted successfully");
};

// Update Voucher
const updateVoucher = async () => {
  if (["JVR", "BPV"].includes(form.value.voucherType)) {
    if (debitTotal.value !== creditTotal.value) {
      showNotification("error", "Debit balance is not equal to Credit balance");
      return;
    }
  }

  if (voucherEntries.value.length === 0) {
    showNotification("error", "Please add at least one entry");
    return;
  }

  const userInfo = JSON.parse(localStorage.getItem("user_info"));

  const payload = {
    SiteCode: siteCode.value || "02",
    Period: period.value || dayjs(form.value.date).format("YYYYMM"),
    JVType: form.value.voucherType,
    JVCat: form.value.category || "",
    JVDate: form.value.date.format("YYYY-MM-DD HH:mm:ss.SSS"),
    TransDate: dayjs().format("YYYY-MM-DD HH:mm:ss.SSS"),
    Posted: 0,
    UserId: userInfo?.UserId || "system",
    EditUserID: userInfo?.UserId || "system",
    EditDate: dayjs().format("YYYY-MM-DD HH:mm:ss.SSS"),
    details: voucherEntries.value.map((e) => ({
      AMCode: e.account_head,
      ASCode: e.type || e.ASCode || "0",
      Person: e.person || "",
      ChequeNo: e.chequeNo || "",
      ChequeName: e.chequeName || "",
      Narration: e.narration || "",
      Debit: Number(e.debit) || 0,
      Credit: Number(e.credit) || 0,
      AntiCode: e.account_head,
      AntiSCode: "0",
      ChequePayDate: dayjs().format("YYYY-MM-DD HH:mm:ss.SSS"),
      ChequeStatus: "Pending",
      VendorId: "123456",
      BillNo: e.billNo || "",
      BillDate: e.billDate
        ? dayjs(e.billDate).format("YYYY-MM-DD HH:mm:ss.SSS")
        : dayjs().format("YYYY-MM-DD HH:mm:ss.SSS"),
      TransType: "pu",
      ChallanNo: e.billNo || "",
      ChallanDate: e.billDate
        ? dayjs(e.billDate).format("YYYY-MM-DD HH:mm:ss.SSS")
        : dayjs().format("YYYY-MM-DD HH:mm:ss.SSS"),
      CertificateNo: "123",
      CertificateIssueDate: "2025-09-10",
      VATAmount: 0,
      TaxPerc: 0,
      TaxPercStd: 0,
      ...(e.RecNo && { RecNo: e.RecNo }),
    })),
  };

  creating.value = true;
  try {
    const res = await axios.put(
      `${apiBase}/journal-master?Period=${period.value}&JVNo=${jvNo.value}&SiteCode=${siteCode.value}`,
      payload,
      getToken()
    );
    showNotification(
      "success",
      res.data.message || "Voucher updated successfully"
    );
    await voucher_idwise();
  } catch (err) {
    console.error("Error updating voucher:", err.response?.data || err);
    showNotification(
      "error",
      err.response?.data?.message || "Failed to update voucher. See console."
    );
  } finally {
    creating.value = false;
  }
};

// Watchers
// watch(
//   () => form.value.voucherType,
//   (newVal) => {
//     fetchCategories(newVal);
//   },
//   { immediate: true }
// );

watch(
  () => form.value.group,
  (newVal) => {
    if (newVal) {
      fetchType(newVal);
    }
  }
);

watch(
    () => form.value.voucherType,
    (newVal) => {
      fetchCategories(newVal);
      // Auto-disable category for JVR
      if (newVal === "JVR") {
        form.value.category = "J";
      }
      // Auto-check Contra if voucher type includes "Bank"
      const selected = voucherTypes.value.find(v => v.JVType === newVal);
      if (selected && /bank/i.test(selected.JVDetails || selected.JVType)) {
        isContra.value = true;
      } else {
        isContra.value = false;
      }
    },
    { immediate: true }
);

watch(
    () => form.value.vendorId,
    (newVal) => {
      if (!newVal) return;
      const vendor = vendorList.value.find(v => v.VendorId === newVal);
      if (vendor) {
        form.value.vendorName = vendor.VendorName;
        form.value.TINNo = vendor.TINNo;
        form.value.BankName = vendor.BankName;
        form.value.AccountNo = vendor.AccountNo;
        form.value.BranchName = vendor.BranchName;
      }
    }
);
const fetchVoucherNumber = async () => {
  const { voucherType, category, date } = form.value;

  if (!voucherType || !category || !date) {
    form.value.voucherNumber = 0;
    return;
  }

  try {
    const period = dayjs(date).format("YYYYMM");

    const { data } = await axios.get(
        `${apiBase}/voucher-no?JVType=${voucherType}&Period=${period}&JVCat=${category}`,
        getToken()
    );

    if (data && data.JVSerial !== undefined) {
      form.value.voucherNumber = data.JVSerial;
    } else {
      console.warn("Unexpected response from /voucher-no:", data);
      form.value.voucherNumber = 0;
    }
  } catch (err) {
    console.error("Error fetching voucher number:", err);
    form.value.voucherNumber = 0;
  }
};

watch(
    [() => form.value.voucherType, () => form.value.category, () => form.value.date],
    ([newType, newCat, newDate]) => {
      if (newType && newCat && newDate) {
        fetchVoucherNumber();
      }
    }
);

const vendorList = ref([]);

const fetchVendors = async () => {
  try {
    const res = await axios.get(`${apiBase}/settings/vendor-list/all`, getToken());
    console.log('res', res)
    if (res.data.success) {
      vendorList.value = res.data.data.filter(v => v.Active === "1");
    } else {
      showNotification("error", "Failed to fetch vendor list");
    }
  } catch (err) {
    console.error(err);
    showNotification("error", "Error fetching vendor list");
  }
};

const fetchChequeRegisterList = async () => {
  try {
    const res = await axios.get(`${apiBase}/settings/cheque-register-details/all`, getToken());
    if (res.data.success) {
      // Filter only ACTIVE status cheques
      const activeCheques = res.data.data.filter(c => c.Status === "ACTIVE");
      chequeRegisterList.value = activeCheques;
      all_chequeRegisterList.value = activeCheques;
    }
  } catch (err) {
    console.error("Error fetching cheque register list:", err);
  }
};

const filterVendor = (input, option) => {
  const text = input.toLowerCase();
  const vendor = vendorList.value.find(v => v.VendorId === option.value);
  if (!vendor) return false;
  return (
      vendor.VendorId.toLowerCase().includes(text) ||
      vendor.VendorName.toLowerCase().includes(text)
  );
};


const inputRefs = [
  date_ref,
  voucher_type_ref,
  category_ref,
  group_code_ref,
  type_ref,
  account_head_ref,
  sub_ledger_ref,
  person_ref,
  cheque_no_ref,
  cheque_name_ref,
  bill_no_ref,
  bill_date_ref,
  narration_ref,
  debit_ref,
  credit_ref,
];
let currentIndex = -1;

// Update currentIndex on focus
inputRefs.forEach((refEl, index) => {
  watch(refEl, () => {
    if (refEl.value) {
      refEl.value?.$el?.addEventListener("focusin", () => {
        currentIndex = index;
      });
    }
  });
});

// Handle ESC key (go back)
const handleKeydown = (e) => {
  if (e.key === "Escape" && currentIndex > 0) {
    e.preventDefault();
    const prevRef = inputRefs[currentIndex - 1];
    if (prevRef?.value?.focus) {
      prevRef.value.focus();
    } else if (prevRef?.value?.$el?.focus) {
      prevRef.value.$el.focus();
    }
  }
};


onMounted(async () => {
  await fetchVoucherTypes();
  await fetchSubLedgerList();
  await fetchgroup();
  await fetchAccount_head();
  await voucher_idwise();
  await fetchVendors();
  await fetchChequeRegisterList();
  window.addEventListener("keydown", handleKeydown);
});

onUnmounted(() => {
  window.removeEventListener("keydown", handleKeydown);
});

</script>

<style>
.ant-input-number-input {
  @apply !text-right !pr-10;
}

input:focus,
.ant-input:focus,
.ant-select-focused .ant-select-selector,
.ant-input-number-focused .ant-input-number-input > input {
  background-color: #fff8c4 !important; /* light yellow */
  transition: background-color 0.2s ease;
}
</style>
