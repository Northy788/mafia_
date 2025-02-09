<template>
  <div class="min-h-screen bg-gray-100">
    <Navbar />
    <main class="container mx-auto py-10">
      <!-- Filters Section -->
      <div class="mb-4 flex flex-wrap gap-4 bg-white p-4 rounded-lg shadow-md">
        <!-- Fund Type Checkboxes -->
        <div class="flex flex-col space-y-2">
          <label class="text-sm font-medium">ประเภทกองทุนรวม:</label>
          <div class="flex flex-wrap gap-4">
            <label v-for="type in fundTypes" :key="type" class="flex items-center space-x-2">
              <input type="checkbox" v-model="selectedFundTypes" :value="type"
                class="form-checkbox rounded border-gray-300">
              <span class="text-sm">{{ type }}</span>
            </label>
          </div>
        </div>

        <!-- Tax Saving Fund Radio -->
        <div class="flex flex-col space-y-2">
          <label class="text-sm font-medium">กองทุนลดหย่อนภาษี:</label>
          <div class="flex gap-4">
            <label class="flex items-center space-x-2">
              <input type="radio" v-model="selectedTaxSaving" value="ทั้งหมด" class="form-radio border-gray-300">
              <span class="text-sm">ทั้งหมด</span>
            </label>
            <label v-for="type in taxSavingTypes" :key="type" class="flex items-center space-x-2">
              <input type="radio" v-model="selectedTaxSaving" :value="type" class="form-radio border-gray-300">
              <span class="text-sm">{{ type }}</span>
            </label>
          </div>
        </div>

        <!-- Date Range Inputs -->
        <div class="flex items-center space-x-2">
          <label class="text-sm">ระยะเวลา:</label>
          <input type="date" v-model="dateRange.start" :max="dateRange.end" class="form-input rounded border p-1">
          <span>ถึง</span>
          <input type="date" v-model="dateRange.end" :min="dateRange.start" class="form-input rounded border p-1">
          <button @click="resetDateRange" class="px-2 py-1 bg-gray-200 rounded hover:bg-gray-300">
            ล้างข้อมูล
          </button>
        </div>
      </div>

      <!-- Table Section -->
      <table class="w-full bg-white border rounded-lg shadow-md overflow-hidden">
        <thead>
          <tr class="bg-green-500 text-white">
            <th v-for="column in columns" :key="column.key" class="py-2 px-4 text-sm">
              <span class="cursor-pointer" @click="sort(column.key)">
                {{ column.label }}
                <span v-if="sortKey === column.key">
                  {{ sortOrder === 1 ? '▼' : '▲' }}
                </span>
                <span v-else>▼</span>
              </span>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="fund in paginatedFunds" :key="fund.fundName"
            class="odd:bg-gray-100 even:bg-white hover:bg-gray-300">
            <td class="py-2 px-4 text-center">{{ fund.fund_name }}</td>
            <td class="py-2 px-4 text-center">{{ transaction_type_str(fund.transaction_type) }}</td>
            <td class="py-2 px-4 text-center">{{ fund.gain_loss_percent }}</td>
            <td class="py-2 px-4 text-center">{{ fund.units_processed }}</td>
            <td class="py-2 px-4 text-center">{{ fund.processed_nav }}</td>
            <td class="py-2 px-4 text-center">{{ fund.amount_processed }}</td>
            <td class="py-2 px-4 text-center">{{ formatDate(fund.transaction_date) }}</td>
          </tr>
        </tbody>
      </table>

      <!-- Pagination -->
      <div class="flex justify-around items-center mt-4">
        <button @click="prevPage" :disabled="currentPage === 1"
          class="px-4 py-2 bg-orange-400 hover:bg-orange-500 text-white rounded-md disabled:opacity-50 disabled:cursor-not-allowed">
          🡄 ย้อนกลับ
        </button>
        <span class="text-white">หน้า {{ currentPage }}</span>
        <button @click="nextPage" :disabled="currentPage === totalPages"
          class="px-4 py-2 bg-orange-400 hover:bg-orange-500 text-white rounded-md disabled:opacity-50 disabled:cursor-not-allowed">
          ต่อไป 🡆
        </button>
      </div>
    </main>
  </div>
</template>

<script>
import { ref, computed, watch } from 'vue'
import Navbar from '@/components/Navbar.vue'

export default {
  name: "FundTable",
  components: {
    Navbar
  },
  data() {
    return {
      columns: [
        { key: "fund_name", label: "ชื่อกองทุน" },
        { key: "transaction_type", label: "ประเภทธุรกรรม" },
        { key: "gain_loss_percent", label: "%G/L" },
        { key: "units_processed", label: "จำนวนหน่วย" },
        { key: "processed_nav", label: "NAV" },
        { key: "amount_processed", label: "จำนวนเงิน" },
        { key: "transaction_date", label: "วันที่" }
      ],
      funds: [],
      fundTypes: [
        "กองทุนรวมตลาดเงินในประเทศ",
        "กองทุนรวมตลาดเงินต่างประเทศ",
        "กองทุนรวมพันธบัตรรัฐบาล",
        "กองทุนรวมตราสารหนี้",
        "กองทุนรวมผสม",
        "กองทุนรวมตราสารทุน",
        "กองทุนรวมหมวดอุตสาหกรรม",
        "กองทุนรวมสินทรัพย์ทางเลือก"
      ],
      taxSavingTypes: ["RMF", "SSF", "ThaiESG"],
      selectedFundTypes: [],
      selectedTaxSaving: "ทั้งหมด",
      dateRange: { start: null, end: null },
      currentPage: 1,
      perPage: 10,
      sortKey: "fundName",
      sortOrder: 1
    };
  },
  computed: {
    filteredData() {
      let result = this.funds;

      if (this.selectedFundTypes.length > 0) {
        result = result.filter(fund => this.selectedFundTypes.includes(fund.type));
      }

      if (this.selectedTaxSaving !== "ทั้งหมด") {
        result = result.filter(fund => fund.taxSaving === this.selectedTaxSaving);
      }

      if (this.dateRange.start && this.dateRange.end) {
        const startDate = new Date(this.dateRange.start);
        const endDate = new Date(this.dateRange.end);
        startDate.setHours(0, 0, 0, 0);
        endDate.setHours(23, 59, 59, 999);

        result = result.filter(fund => {
          const fundDate = new Date(fund.date);
          return fundDate >= startDate && fundDate <= endDate;
        });
      }

      return result;
    },
    sortedFunds() {
      const filtered = [...this.filteredData];

      return filtered.sort((a, b) => {
        const valueA = a[this.sortKey];
        const valueB = b[this.sortKey];

        if (!valueA && valueB) return 1;
        if (valueA && !valueB) return -1;
        if (!valueA && !valueB) return 0;

        if (this.sortKey === "date") {
          return (new Date(valueA) - new Date(valueB)) * this.sortOrder;
        }

        if (this.sortKey === "type") {
          return valueA.localeCompare(valueB, "th") * this.sortOrder;
        }

        if (this.sortKey === "gainLoss") {
          const numA = parseFloat(valueA);
          const numB = parseFloat(valueB);
          return (numA - numB) * this.sortOrder;
        }

        if (typeof valueA === "number" && typeof valueB === "number") {
          return (valueA - valueB) * this.sortOrder;
        }

        return valueA.localeCompare(valueB, "th") * this.sortOrder;
      });
    },
    paginatedFunds() {
      const startIndex = (this.currentPage - 1) * this.perPage;
      const endIndex = Math.min(startIndex + this.perPage, this.sortedFunds.length);
      return this.sortedFunds.slice(startIndex, endIndex);
    },
    totalPages() {
      return Math.ceil(this.sortedFunds.length / this.perPage);
    }
  },
  methods: {
    transaction_type_str(type) {
      if (type == 1) return "ซื้อ"
      if (type == 2) return "ขาย"
    },
    formatDate(date) {
      return new Date(date).toLocaleDateString("th-TH", {
        year: "numeric",
        month: "long",
        day: "numeric"
      });
    },
    formatValue(value, unit = "") {
      if (value === null || value === undefined || value === "") {
        return "-";
      }
      return unit ? `${value} ${unit}` : value;
    },
    sort(key) {
      if (this.sortKey === key) {
        this.sortOrder *= -1;
      } else {
        this.sortKey = key;
        this.sortOrder = 1;
      }
      this.currentPage = 1;
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    resetDateRange() {
      this.dateRange = { start: null, end: null };
    },
    async fetchData() {
      const jwtToken = localStorage.getItem('jwtToken');
      const username = localStorage.getItem('username');
      console.log("fetch api");
      try {
        // Fetch ข้อมูล fund หลัก
        const response = await fetch(`/api/transaction/user/${username}`, {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
            "Authorization": `Bearer ${jwtToken}`
          },
        });
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        console.log("Fetched API data:", data);
        this.funds = data;
      } catch (error) {
        console.error("Failed to fetch API data:", error);
      }
    },
  },
  mounted() {
    this.fetchData();
    // document.addEventListener("click", this.handleClickOutside);
  },
  watch: {
    selectedFundTypes: {
      handler() {
        this.currentPage = 1;
      },
      deep: true
    },
    selectedTaxSaving() {
      this.currentPage = 1;
    },
    dateRange: {
      handler() {
        this.currentPage = 1;
      },
      deep: true
    }
  }
}
</script>

<style>
.min-h-screen {
  background-image: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)), url('@/assets/images/bg1.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  position: relative;
}
</style>