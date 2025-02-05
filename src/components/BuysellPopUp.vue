<template>
    <div>
      <!-- Modal Popup -->
        <div v-if="showModal" class="fixed inset-0 flex items-center justify-center z-50">
          <!-- Overlay: คลิกที่พื้นหลังเพื่อปิด Modal -->
          <div class="fixed inset-0 bg-black opacity-50" @click="closeModal"></div>
          
          <!-- Card Component -->
          <div class="relative bg-white rounded-lg shadow-lg overflow-hidden w-full max-w-md mx-auto z-50">
            <!-- Header: แถบสี -->
            <div class="bg-blue-600 p-4">
              <h3 class="text-lg font-semibold text-white">Header Card</h3>
            </div>
            <!-- Body: เนื้อหาของ Modal -->
            <div class="p-4">
              <p class="text-gray-700">
                นี่คือเนื้อหาใน modal popup card component คุณสามารถปรับแต่งเนื้อหาในส่วนนี้ได้ตามต้องการ
              </p>
            </div>
            <!-- Footer: ปุ่มปิด Modal -->
            <div class="p-4 flex justify-end">
              <button @click="closeModal" class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600">
                ปิด
              </button>
            </div>
          </div>
        </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'ModalPopup',
    data() {
      return {
        showModal: false
      };
    },
    methods: {
      openModal() {
        this.showModal = true;
      },
      closeModal() {
        this.showModal = false;
      }
    }
  };
  </script>
  
  <style scoped>
  .modal-enter-active,
  .modal-leave-active {
    transition: opacity 0.3s;
  }
  .modal-enter-from,
  .modal-leave-to {
    opacity: 0;
  }
  </style>
  