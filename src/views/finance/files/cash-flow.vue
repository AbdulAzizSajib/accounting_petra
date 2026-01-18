<template>
  <MainLayout>
    <div class="flex justify-between">
      <div class="mb-4">
        <a-input
          placeholder="Search here..."
          v-model:value="search"
          @input="
            async () => {
              page = 1;
              await fetchAllData();
            }
          "
          class="w-64"
        />
      </div>
      <div class="mb-4">
        <button
          class="bg-primary text-white px-4 py-2 rounded"
          @click="isCreateModalVisible = true"
        >
          Add Cash Flow
        </button>
      </div>
    </div>
    <h1 class="text-2xl font-bold text-primary flex gap-3 mb-4">
      Cash Flow ({{ total }})
      <Icon v-if="isShowLoading" class="size-7" icon="line-md:loading-loop" />
    </h1>

    <!-- Table -->
    <table class="w-full border border-collapse text-left">
      <thead>
        <tr class="bg-primary text-white">
          <th class="border border-white px-4 py-2">Cash Flow ID</th>
          <th class="border border-white px-4 py-2">Details</th>
          <th class="border border-white px-4 py-2 text-center w-8">Actions</th>
        </tr>
      </thead>
      <tbody class="capitalize">
        <tr v-for="(data, index) in allData" :key="index">
          <td class="px-4 border">{{ data?.CashFlowId }}</td>
          <td class="px-4 border">{{ data?.Details }}</td>

          <td class="px-4 border text-center w-8">
            <div class="flex justify-center gap-x-3">
              <button
                @click="allData_idwise(data?.CashFlowId)"
                type="button"
                class="px-2 py-1 bg-secondary text-white rounded-md hover:bg-primary"
              >
                <i class="bi bi-pencil"></i>
              </button>

              <!-- <a-popconfirm
                @confirm="deleteData(data?.CashFlowId)"
                title="Are you sure?"
                ok-text="Yes"
                cancel-text="No"
              >
                <button
                  type="button"
                  class="px-2 py-1 bg-danger text-white rounded-md hover:bg-dangerDark"
                >
                  <i class="bi bi-trash3"></i>
                </button>
              </a-popconfirm> -->
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Create Modal -->
    <a-modal
      v-model:open="isCreateModalVisible"
      title="Cash Flow"
      @cancel="isCreateModalVisible = false"
      :footer="null"
    >
      <form @submit.prevent="createallData">
        <div class="grid grid-cols-1 gap-x-4 custom-form">
          <!-- Details Field -->
          <a-form-item
            label="Details"
            name="Details"
            :rules="[{ required: false, message: 'Please input details!' }]"
          >
            <a-input
              class="w-full"
              placeholder="Enter Details"
              v-model:value="formData.Details"
            />
          </a-form-item>
        </div>

        <!-- @click="createallData" -->
        <div class="flex items-center justify-end">
          <button
            type="submit"
            class="border px-6 py-1 rounded font-semibold bg-primary text-white"
          >
            Save
          </button>
        </div>
      </form>
    </a-modal>

    <!-- Edit Modal -->
    <a-modal
      :footer="null"
      v-model:open="isEditModalVisible"
      title="Edit cash flow"
      @cancel="isEditModalVisible = false"
      width="500px"
    >
      <form @submit.prevent="updateallData(updateformData.CashFlowId)">
        <div class="grid grid-cols-1 gap-x-4 custom-form">
          <!-- Group Details Field -->
          <a-form-item
            label="Details"
            name="Details"
            :rules="[{ required: false, message: 'Please input details!' }]"
          >
            <a-input
              class="w-full"
              placeholder="Enter Details"
              v-model:value="updateformData.Details"
            />
          </a-form-item>
        </div>

        <div class="flex items-center justify-end">
          <button
            type="submit"
            class="border px-6 py-1 rounded font-semibold bg-primary text-white"
          >
            Update
          </button>
        </div>
      </form>
    </a-modal>

    <div
      v-if="loading"
      class="mt-2 text-center text-gray-500 flex justify-center items-center gap-2"
    >
      <span><a-spin></a-spin></span>
    </div>

    <a-pagination
      class="mt-4"
      v-model:current="page"
      :page-size="per_page"
      :total="total"
      :show-size-changer="false"
      :show-total="(total) => `Total ${total} items`"
      @change="
        (pageNo) => {
          page = pageNo;
          fetchAllData();
        }
      "
      v-if="total > per_page"
    />
  </MainLayout>
</template>

<script setup>
import { onMounted, ref } from "vue";
import { Icon } from "@iconify/vue";
import MainLayout from "@/components/layouts/mainLayout.vue";
import { apiBase } from "@/config";
import axios from "axios";
import { getToken, showNotification } from "@/utilities/common";

import { useRouter } from "vue-router";
import debounce from "debounce";
const router = useRouter();
const goBack = () => {
  router.push({ name: "overview" });
};

const isCreateModalVisible = ref(false);
const isEditModalVisible = ref(false);
const isCreating = ref(false);
const isUpdating = ref(false);

const formData = ref({
  Details: "",
});
const updateformData = ref({
  CashFlowId: "",
  Details: "",
});

// create
const createallData = async () => {
  isCreating.value = true;
  try {
    const res = await axios.post(
      `${apiBase}/settings/cashflow`,
      formData.value,
      getToken(),
    );
    isCreating.value = false;

    if (res.data.success === true) {
      showNotification(
        res?.data?.success ? "success" : "error",
        res?.data?.message,
      );
      await fetchAllData();
      formData.value = {
        Details: "",
      };
    }

    isCreateModalVisible.value = false;
  } catch (error) {
    isCreating.value = false;
    isCreateModalVisible.value = false;
    showNotification("error", error?.response?.data?.message || error?.message);
  }
};

const isShowLoading = ref(false);
const allData_idwise = async (id) => {
  try {
    isShowLoading.value = true;
    const res = await axios.get(
      `${apiBase}/settings/cashflow/show?id=${id}`,
      getToken(),
    );
    isShowLoading.value = false;

    updateformData.value = {
      Details: res?.data?.data?.Details,
      CashFlowId: res?.data?.data?.CashFlowId,
    };
    isEditModalVisible.value = true;
  } catch (error) {
    showNotification("error", error?.response?.data?.message || error?.message);
  }
};

// update
const updateallData = async (GroupCode) => {
  const finalFormData = {
    Details: updateformData.value.Details,
  };
  isUpdating.value = true;
  try {
    const res = await axios.put(
      `${apiBase}/settings/cashflow?id=${GroupCode}`,
      finalFormData,
      getToken(),
    );
    isUpdating.value = false;
    showNotification(
      res?.data?.success ? "success" : "error",
      res?.data?.message,
    );

    createallData;
    await fetchAllData();
    isEditModalVisible.value = false;
  } catch (error) {
    isUpdating.value = false;
    isEditModalVisible.value = false;
    showNotification("error", error?.response?.data?.message || error?.message);
  }
};

let searchTimeout = null;

const page = ref(1);
const per_page = ref(10);
const total = ref(0);
const search = ref("");

const allData = ref([]);
const loading = ref(false);

// Fetch allData with search and pagination
const fetchAllData = async () => {
  loading.value = true;
  try {
    const res = await axios.get(
      `${apiBase}/settings/cashflow?page=${page.value}&per_page=${per_page.value}&search=${search.value}`,
      getToken(),
    );
    console.log(res.data);
    loading.value = false;
    allData.value = res?.data?.data?.data;
    total.value = res?.data?.data?.total;
  } catch (err) {
    loading.value = false;
    allData.value = [];
    total.value = 0;
    console.error("Failed to fetch allData:", err);
  }
};

const deleteData = async (groupcode) => {
  loading.value = true;
  try {
    const res = await axios.delete(
      `${apiBase}/settings/cashflow?id=${groupcode}`,
      getToken(),
    );
    showNotification(res?.data ? "success" : "error", res?.data?.message);
    await fetchAllData();
  } catch (err) {
    loading.value = false;
    showNotification("error", error?.response?.data?.message || error?.message);
  }
};
const searchLoading = ref(false);

const detailsList = ref([]);
const getDetails = debounce(async (event) => {
  searchLoading.value = true;
  try {
    const res = await axios.get(
      `${apiBase}/settings/cashflow/all?search=${event || " "}`,
      getToken(),
    );
    searchLoading.value = false;
    if (res?.data) {
      detailsList.value = res?.data?.data;
      console.log(detailsList.value);
    }
  } catch (error) {
    console.log(error.message);
    searchLoading.value = false;
  }
}, 500);

onMounted(async () => {
  await fetchAllData();
  await getDetails();
});
</script>

<style>
.ant-input-number-input {
  @apply !text-right !pr-10;
}
</style>
