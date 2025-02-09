<template>
  <div class="max-w-lg mx-auto bg-white rounded-lg shadow-lg overflow-hidden animate-fadeIn">
    
    <!-- ✅ Tab Selector for Buy/Sell -->
    <div class="flex">
      <button @click="changeTransactionType('buy')"
        :class="['flex-1 py-3 text-lg font-semibold text-center', transactionType === 'buy' ? 'bg-green-500 text-white' : 'bg-gray-200 text-gray-600']">
        ซื้อ
      </button>
      <button @click="changeTransactionType('sell')"
        :class="['flex-1 py-3 text-lg font-semibold text-center', transactionType === 'sell' ? 'bg-red-500 text-white' : 'bg-gray-200 text-gray-600']">
        ขาย
      </button>
    </div>

    <!-- ✅ Dynamic Header -->
    <div :class="headerClass" class="text-white py-4 px-6 text-lg font-bold text-center">
      ประเภทรายการ: <span class="font-extrabold">{{ transactionText }}</span>
    </div>

    <div class="p-6">
      <!-- ✅ Input Fields -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <!-- Fund Name (Full Width) -->
        <div class="md:col-span-2">
          <label class="block text-gray-700 font-medium mb-1">ชื่อกองทุน</label>
          <p class="w-full p-3 border rounded-md bg-gray-100 text-gray-600">{{ name }}</p>
        </div>

        <!-- Amount (บาท) -->
        <div>
          <label class="block text-gray-700 font-medium mb-1">จำนวนเงิน (บาท)</label>
          <input type="number" class="w-full p-3 border rounded-md text-right focus:ring-2 focus:ring-teal-500"
            v-model="amount_processed" />
        </div>

        <!-- Price per Unit -->
        <div>
          <label class="block text-gray-700 font-medium mb-1">ราคาต่อหน่วย (บาท)</label>
          <input type="number" class="w-full p-3 border rounded-md text-right focus:ring-2 focus:ring-teal-500"
            v-model="processed_nav" />
        </div>

        <!-- Number of Units -->
        <div>
          <label class="block text-gray-700 font-medium mb-1">จำนวนหน่วย (หน่วย)</label>
          <input type="number" class="w-full p-3 border rounded-md text-right focus:ring-2 focus:ring-teal-500"
            v-model="units_processed" />
        </div>

        <!-- Transaction Date -->
        <div>
          <label class="block text-gray-700 font-medium mb-1">วันที่ดำเนินการ</label>
          <input type="date" class="w-full p-3 border rounded-md focus:ring-2 focus:ring-teal-500"
            v-model="transaction_date" />
        </div>
      </div>

      <!-- ✅ Action Buttons -->
      <div class="mt-6 flex space-x-4">
        <button @click="confirmCancel"
          class="w-1/2 bg-gray-500 text-white py-3 rounded-lg font-semibold hover:bg-gray-600 transition">
          ยกเลิก
        </button>
        <button class="w-1/2 py-3 rounded-lg font-semibold transition"
          :class="transactionType === 'buy' ? 'bg-green-500 hover:bg-green-600 text-white' : 'bg-red-500 hover:bg-red-600 text-white'"
          @click="confirmTransaction">
          ยืนยัน
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "BuySellPopUp",
  props: {
    name: {
      type: String,
      default: "N/A"
    },
    fundType: {
      type: String,
      default: "N/A"
    }
  },
  data() {
    return {
      username: "",
      transactionType: "buy", // Default to "buy"
      amount_processed: "",
      processed_nav: "",
      units_processed: "",
      transaction_date: new Date().toISOString().split("T")[0],
      hasData: false // ตรวจสอบว่ามีข้อมูลที่กรอกหรือไม่
    };
  },
  computed: {
    headerClass() {
      return this.transactionType === "buy" ? "bg-green-500" : "bg-red-500";
    },
    transactionText() {
      return this.transactionType === "buy" ? "ซื้อ" : "ขาย";
    },
    transactionEnum() {
      return this.transactionType === "buy" ? 1 : 2;
    }
  },
  watch: {
    amount_processed() {
      this.checkData();
    },
    processed_nav() {
      this.checkData();
    },
    units_processed() {
      this.checkData();
    },
    transaction_date() {
      this.checkData();
    }
  },
  methods: {
    checkData() {
      this.hasData = !!(this.amount_processed || this.processed_nav || this.units_processed);
    },
    async confirmTransaction() {
      if (!this.amount_processed || !this.processed_nav || !this.units_processed || !this.transaction_date) {
        alert("กรุณากรอกข้อมูลให้ครบก่อนทำรายการ");
        return;
      }
      const jwtToken = localStorage.getItem('jwtToken');
      this.username = localStorage.getItem('username');
      const apiUrl = this.transactionType === "buy" 
        ? "/api/transaction/buy" 
        : "/api/transaction/sell";

      const payload = {
        username: this.username,
        fund_name: this.name,
        transaction_type: this.transactionEnum,
        transaction_date: this.transaction_date,
        units_processed: parseFloat(this.units_processed),
        amount_processed: parseFloat(this.amount_processed),
        processed_nav: parseFloat(this.processed_nav),
        fund_type: this.fundType
      };

      try {
        const response = await fetch(apiUrl, {
          method: "POST",
          headers: { 
            "Content-Type": "application/json",
            "Authorization": `Bearer ${jwtToken}`
          },
          body: JSON.stringify(payload)
        });

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const result = await response.json();
        alert(`✅ ทำรายการสำเร็จ: ${result.message}`);
        this.$emit("close");
      } catch (error) {
        console.error("❌ เกิดข้อผิดพลาดในการส่งข้อมูล:", error);
        alert("❌ เกิดข้อผิดพลาดในการทำรายการ กรุณาลองอีกครั้ง");
      }
    },
    confirmCancel() {
      if (this.hasData) {
        if (confirm("คุณแน่ใจหรือไม่ว่าต้องการยกเลิกการทำรายการนี้?")) {
          this.$emit("close");
        }
      } else {
        this.$emit("close");
      }
    },
    changeTransactionType(type) {
      if (this.transactionType === type) return;

      if (this.hasData) {
        if (confirm("การเปลี่ยนประเภทการทำรายการจะล้างข้อมูลที่กรอกไปแล้ว คุณแน่ใจหรือไม่?")) {
          this.clearForm();
          this.transactionType = type;
        }
      } else {
        this.transactionType = type;
      }
    },
    clearForm() {
      this.amount_processed = "";
      this.processed_nav = "";
      this.units_processed = "";
      this.transaction_date = new Date().toISOString().split("T")[0];
      this.hasData = false;
    }
  }
};
</script>

<style scoped>
/* ✅ Smooth Fade-in Animation */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
.animate-fadeIn {
  animation: fadeIn 0.3s ease-out;
}
</style>
