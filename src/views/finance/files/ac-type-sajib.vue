<template>
  <mainLayout>
  
    <div class="flex justify-between">
      <div class="mb-4">
        <a-input placeholder="Search here..." />
      </div>
      <div class="mb-4">
        <button
          class="bg-primary text-white px-4 py-2 rounded"
          @click="isCreateModalVisible = true"
        >
          Add AC Type
        </button>
      </div>
    </div>
    <h1 class="text-2xl font-bold text-primary mb-4">Salary Group Code (0)</h1>

    <!-- Table -->
    <table class="w-full border border-collapse text-left">
      <thead>
        <tr class="bg-primary text-white">
          <th class="border border-white px-4 py-2">Group Code</th>
          <th class="border border-white px-4 py-2">Group Details</th>
          <th class="border border-white px-4 py-2">Report Order</th>
          <th class="border border-white px-4 py-2 text-center">Actions</th>
        </tr>
      </thead>
      <tbody class="capitalize">
        <tr>
          <td class="px-4 border">EMP001</td>
          <td class="px-4 border">John Doe</td>
          <td class="px-4 border">Lorem ipsum dolor sit amet.</td>
          <td class="px-4 border text-center w-8">
            <div class="flex justify-center gap-x-3">
              <button
                type="button"
                class="px-2 py-1 bg-secondary text-white rounded-md hover:bg-primary"
                @click="isEditModalVisible = true"
              >
                <i class="bi bi-pencil"></i>
              </button>
              <a-popconfirm
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
              </a-popconfirm>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Create Modal -->
    <a-modal
      v-model:open="isCreateModalVisible"
      title="A/C Type"
      @cancel="isCreateModalVisible = false"
      width="1000px"
    >
      <a-form>
        <div class="grid grid-cols-1 gap-x-4">
          <!-- Employee Code -->
          <div class="mb-4">
            <label class="block font-semibold mb-1">A/C Type Code</label>
            <a-input class="w-full" placeholder="A/C Type Code" />
          </div>

          <!-- Employee Name -->
          <div class="mb-4">
            <label class="block font-semibold mb-1">A/C Type Details</label>
            <textarea
              rows="3"
              class="w-full border resize-none"
              placeholder=""
            />
          </div>

          <!-- Group Code -->
          <div class="mb-4">
            <label class="block font-semibold mb-1">Group Code</label>
            <a-select class="w-full" placeholder="Group Code" />
          </div>
        </div>
      </a-form>
    </a-modal>

    <!-- Edit Modal -->
    <a-modal
      v-model:open="isEditModalVisible"
      title="Edit Salary Adjustment"
      @cancel="isEditModalVisible = false"
      width="1000px"
    >
      <a-form>
        <div class="grid grid-cols-1 gap-x-4">
          <!-- Employee Code -->
          <div class="mb-4">
            <label class="block font-semibold mb-1">A/C Type Code</label>
            <a-input class="w-full" placeholder="A/C Type Code" />
          </div>

          <!-- Employee Name -->
          <div class="mb-4">
            <label class="block font-semibold mb-1">A/C Type Details</label>
            <textarea
              rows="3"
              class="w-full border resize-none"
              placeholder=""
            />
          </div>

          <!-- Group Code -->
          <div class="mb-4">
            <label class="block font-semibold mb-1">Group Code</label>
            <a-select class="w-full" placeholder="Group Code" />
          </div>
        </div>
      </a-form>
    </a-modal>
  </mainLayout>
</template>

<script setup>
import { ref } from "vue";
import mainLayout from "@/components/layouts/mainLayout.vue";
import { useRouter } from "vue-router";
const router = useRouter();
const goBack = () => {
  router.push({ name: "overview" });
};
const isCreateModalVisible = ref(false);
const isEditModalVisible = ref(false);
</script>

<style>
.ant-input-number-input {
  @apply !text-right !pr-10;
}
</style>
