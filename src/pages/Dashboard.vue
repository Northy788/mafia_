<template>
  <div class="min-h-screen">
    <Navbar />
    <div class="mx-48 my-10">
      <!-- Grid หลัก แบ่งเป็น 2 แถว -->
      <div class="grid grid-cols-1 gap-4">
        <!-- แถวแรก: Asset Card และ Empty Card สำหรับเนื้อหาอื่น ๆ -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- Asset Card -->
          <div
            class="border border-stroke dark:border-strokedark rounded-xl shadow-md py-4 px-5 bg-white bg-opacity-100 backdrop-filter backdrop-blur-lg">
            <div class="flex items-center justify-between">
              <div class="flex items-center gap-4.5">
                <div>
                  <div class="font-semibold text-lg">
                    มูลค่าสินทรัพย์
                  </div>
                  <div class="font-semibold text-5xl tracking-tight">
                    {{ formatNumber(totalAsset) }}
                  </div>
                </div>
              </div>
              <div class="text-right">
                <p class="mb-0.5 flex items-center justify-end gap-1 text-lg font-medium text-meta-3">
                  {{ formatNumber(totalReturn) }}%
                </p>
                <p class="text-lg font-medium text-meta-3">
                  {{ formatNumber(totalReturn_value) }}
                </p>
              </div>
            </div>
          </div>

          <!-- Empty Card สำหรับเนื้อหาอื่น ๆ -->
          <div
            class="border border-stroke dark:border-strokedark rounded-xl shadow-md py-4 px-5 bg-white bg-opacity-100 backdrop-filter backdrop-blur-lg">
            <!-- คุณสามารถใส่เนื้อหาอื่นๆ ลงใน card นี้ได้ -->
            <div class="text-center text-gray-500 text-lg font-semibold">
              เนื้อหาอื่น ๆ
            </div>
          </div>
        </div>


        <!-- แถวที่สอง: 2 คอลัมน์ -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- คอลัมน์ซ้าย: Tax Reduction Card -->
          <div>
            <TaxReductionCard title="เป้าหมายการลดหย่อนภาษี" currentAmount="30,000.00 บาท" totalTarget="150,000.00 บาท"
              :progressPercentage="80" />
          </div>

          <!-- คอลัมน์ขวา: Portfolio Card -->
          <div
            class="border border-stroke dark:border-strokedark rounded-xl shadow-md px-5 py-4 bg-white bg-opacity-100 backdrop-filter backdrop-blur-lg">
            <div class="flex flex-wrap items-center justify-between gap-2 px-2">
              <div>
                <p class="text-xl font-bold text-black dark:text-white">
                  ภาพรวมพอร์ต
                </p>
              </div>
              
            </div>

            <!-- รายการ FundCard จาก groupedFunds -->
            <div class="flex flex-col h-full max-h-96 overflow-y-auto scrollbar-style mt-5 mb-4">
              <FundCard v-for="(group, index) in groupedFunds" :key="index" :title="group.fund_type"
                :fundCount="group.fundCount + ' กองทุน'" :fundType="group.fund_type"
                :amount="formatCurrency(group.amount)" :percentageValue="formatCurrency(group.percentageValue)"
                :percentage="formatNumber(group.percentage) + '%'" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar';
import FundCard from '@/components/FundCard.vue';
import TaxReductionCard from "@/components/TaxReductionCard.vue";

export default {
  name: "Dashboard",
  components: {
    Navbar,
    FundCard,
    TaxReductionCard
  },
  data() {
    return {
      apiData: [],
      totalAsset: 0,
      totalReturn_value: 0,
      totalReturn: 0,
    };
  },
  computed: {
    totalAssetComputed() {
      return this.apiData.reduce((sum, fund) => sum + fund.holding_value, 0);
    },
    totalReturnComputed() {
      return this.apiData.reduce((sum, fund) => sum + fund.gain_loss_value, 0);
    },
    // Group apiData ตาม fund_type และคำนวณสรุปสำหรับแต่ละกลุ่ม
    groupedFunds() {
      const groups = {};
      this.apiData.forEach(fund => {
        const type = fund.fund_type;
        if (!groups[type]) {
          groups[type] = {
            fund_type: type,
            fundCount: 0,
            amount: 0,
            percentageValue: 0
          };
        }
        groups[type].fundCount += 1;
        groups[type].amount += fund.holding_value;
        groups[type].percentageValue += fund.gain_loss_value;
      });
      return Object.values(groups).map(group => {
        const percentage = group.amount !== 0 ? (group.percentageValue / group.amount) * 100 : 0;
        return {
          ...group,
          percentage
        };
      });
    }
  },
  watch: {
    totalAssetComputed(newVal) {
      this.totalAsset = newVal;
    },
    totalReturnComputed(newVal) {
      this.totalReturn_value = newVal;
      if (this.totalAsset !== 0) {
        this.totalReturn = (newVal / this.totalAsset) * 100;
      } else {
        this.totalReturn = 0;
      }
    }
  },
  methods: {
    async fetchData() {
      const jwtToken = localStorage.getItem('jwtToken');
      if (!jwtToken) {
        console.error("JWT token ไม่ถูกพบใน localStorage");
        return;
      }
      try {
        const response = await fetch("/api/portfolio/user/ohm_admin", {
          method: "GET",
          headers: {
            "Authorization": `Bearer ${jwtToken}`,
          }
        });
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        console.log("Fetched API data:", data);
        this.apiData = data;
      } catch (error) {
        console.error("Failed to fetch API data:", error);
      }
    },
    formatCurrency(value) {
      return new Intl.NumberFormat("th-TH", {
        style: "currency",
        currency: "THB",
        minimumFractionDigits: 2,
        maximumFractionDigits: 2,
      }).format(value);
    },
    formatNumber(num) {
      return Number(num).toFixed(2);
    }
  },
  mounted() {
    this.fetchData();
  }
};
</script>

<style scoped>
/* Scrollbar Container */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

/* Scrollbar Track */
::-webkit-scrollbar-track {
  background: #e0e0e0;
  border-radius: 10px;
}

/* Scrollbar Thumb */
::-webkit-scrollbar-thumb {
  background: #64748b;
  border-radius: 10px;
  border: 2px solid #e0e0e0;
}

/* Scrollbar Thumb Hover */
::-webkit-scrollbar-thumb:hover {
  background: #4b5563;
}

.scrollbar-style {
  scrollbar-width: thin;
  scrollbar-color: #64748b #e0e0e0;
}
</style>
