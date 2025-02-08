<template>
  <div class="min-h-screen flex flex-col">
    <Navbar />
    <div class="grid grid-cols-1 lg:grid-cols-6 lg:grid-rows-[150px_auto] gap-4 mx-4 lg:mx-64 mt-10 mb-10">
      <!-- Card 1: Asset Card -->
      <div
        class="lg:col-span-4 w-full p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-4 dark:bg-gray-800 dark:border-gray-700">
        <div class="flex justify-between mb-2">
          <div>
            <span
              class="bg-blue-100 text-blue-800 text-sm font-medium me-2 px-2.5 py-0.5 rounded dark:bg-blue-900 dark:text-blue-300">ความเสี่ยง</span>
            <span
              class="bg-blue-100 text-blue-800 text-sm font-medium me-2 px-2.5 py-0.5 rounded dark:bg-blue-900 dark:text-blue-300">ลดหย่อนภาษี</span>
          </div>
          <transition name="modal">
            <BuysellPopUp ref="buySellModal" />
          </transition>
          <div id="app" ref="menuContainer" class="relative inline-block">
            <!-- Slide-Up Menu -->
            <transition enter-active-class="transition transform ease-out duration-300"
              enter-from-class="translate-y-4 opacity-0" enter-to-class="translate-y-0 opacity-100"
              leave-active-class="transition transform ease-in duration-200"
              leave-from-class="translate-y-0 opacity-100" leave-to-class="translate-y-4 opacity-0">
              <div v-if="showMenu" class="absolute bottom-full mb-2 flex flex-col space-y-2">
                <button type="button" @click="openModal"
                  class="px-3 py-0.5 text-xs font-medium text-white bg-green-500 rounded-lg hover:bg-green-600 focus:ring-4 focus:outline-none focus:ring-green-300">
                  ซื้อ
                </button>
                <button type="button"
                  class="px-3 py-0.5 text-xs font-medium text-white bg-red-500 rounded-lg hover:bg-red-600 focus:ring-4 focus:outline-none focus:ring-red-300">
                  ขาย
                </button>
              </div>
            </transition>
            <button type="button" @click="toggleMenu"
              class="px-3 py-0.5 text-xs font-medium text-center text-white bg-blue-700 rounded-lg hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">
              ซื้อ-ขาย
            </button>
          </div>
        </div>
        <div class="flex justify-between items-center">
          <div class="text-left">
            <!-- fundName จาก route -->
            <div class="text-2xl">{{ fundName }}</div>
            <div class="text-xl">Full Name</div>
          </div>
          <div class="flex gap-5 items-center">
            <div>
              <div class="text-2xl">1.234567</div>
              <div class="text-xl">0.00 (0.00%)</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Card 2: ผลการดำเนินการย้อนหลัง -->
      <div
        class="lg:col-span-2 lg:row-span-2 w-full p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-4 dark:bg-gray-800 dark:border-gray-700">
        <div class="flex items-center justify-between mb-4">
          <h5 class="text-xl font-bold leading-none text-gray-900 dark:text-white">ผลการดำเนินการย้อนหลัง</h5>
        </div>
        <div>
          <div
            class="w-full max-w-md p-4 bg-white border border-gray-200 rounded-lg shadow-sm sm:p-8 dark:bg-gray-800 dark:border-gray-700">

            <div class="flex items-center justify-between gap-2 mb-4">
              <ul class="flex flex-col p-4 md:p-0 mt-4 font-medium border border-gray-100 rounded-lg bg-gray-50 
             md:space-x-8 rtl:space-x-reverse md:flex-row md:mt-0 md:border-0 md:bg-white 
             dark:bg-gray-800 md:dark:bg-gray-900 dark:border-gray-700">

                <li>
                  <a href="#" @click.prevent="updateContent('return')" :class="['text-xs block py-1 px-3 rounded-sm md:p-0',
                    selectedContent === 'return' ? 'bg-blue-500 text-white' : 'text-gray-900 hover:bg-gray-100']">
                    ผลตอบแทนย้อนหลัง
                  </a>
                </li>

                <li>
                  <a href="#" @click.prevent="updateContent('sd')" :class="['text-xs block py-1 px-3 rounded-sm md:p-0',
                    selectedContent === 'sd' ? 'bg-blue-500 text-white' : 'text-gray-900 hover:bg-gray-100']">
                    ค่าเบี่ยงเบนมาตราฐาน (SD)
                  </a>
                </li>

                <li>
                  <a href="#" @click.prevent="updateContent('sharpeRatio')" :class="['text-xs block py-1 px-3 rounded-sm md:p-0',
                    selectedContent === 'sharpeRatio' ? 'bg-blue-500 text-white' : 'text-gray-900 hover:bg-gray-100']">
                    Sharpe Ratio
                  </a>
                </li>

              </ul>
            </div>


            <div class="flow-root">
              <ul role="list" class="divide-y divide-gray-200 dark:divide-gray-700">

                <li class="py-3 sm:py-4">
                  <div class="flex items-center">
                    <div class="flex-1 min-w-0 ms-4">
                      <p class="text-sm font-semibold text-gray-900 truncate dark:text-white">
                        3 เดือน
                      </p>
                    </div>
                    <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">

                      {{ SummaryData.find(item => item.duration === '3 เดือน')?.percentage }}
                    </div>

                  </div>
                </li>

                <li class="py-3 sm:py-4">
                  <div class="flex items-center">
                    <div class="flex-1 min-w-0 ms-4">
                      <p class="text-sm font-semibold text-gray-900 truncate dark:text-white">
                        6 เดือน
                      </p>
                    </div>

                    <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">

                      {{ SummaryData.find(item => item.duration === '6 เดือน')?.percentage }}

                    </div>

                  </div>
                </li>

                <li class="py-3 sm:py-4">
                  <div class="flex items-center">
                    <div class="flex-1 min-w-0 ms-4">
                      <p class="text-sm font-semibold text-gray-900 truncate dark:text-white">
                        YTD
                      </p>
                    </div>

                    <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">
                      {{ SummaryData.find(item => item.duration === 'YTD')?.percentage }}
                    </div>

                  </div>
                </li>

                <li class="py-3 sm:py-4">
                  <div class="flex items-center">
                    <div class="flex-1 min-w-0 ms-4">
                      <p class="text-sm font-semibold text-gray-900 truncate dark:text-white">
                        3 ปี
                      </p>
                    </div>

                    <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">
                      {{ SummaryData.find(item => item.duration === '3 ปี')?.percentage }}
                    </div>

                  </div>
                </li>

                <li class="py-3 sm:py-4">
                  <div class="flex items-center">
                    <div class="flex-1 min-w-0 ms-4">
                      <p class="text-sm font-semibold text-gray-900 truncate dark:text-white">
                        5 ปี
                      </p>
                    </div>

                    <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">
                      {{ SummaryData.find(item => item.duration === '5 ปี')?.percentage }}
                    </div>

                  </div>
                </li>

                <li class="py-3 sm:py-4">
                  <div class="flex items-center">
                    <div class="flex-1 min-w-0 ms-4">
                      <p class="text-sm font-semibold text-gray-900 truncate dark:text-white">
                        10 ปี
                      </p>
                    </div>

                    <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">
                      {{ SummaryData.find(item => item.duration === '10 ปี')?.percentage }}
                    </div>

                  </div>
                </li>

              </ul>
            </div>
          </div>

        </div>
      </div>

      <!-- Card 3: กราฟและปุ่มเลือก timeframe -->
      <div
        class="lg:col-span-4 w-full bg-white border border-gray-200 rounded-lg shadow sm:p-4 dark:bg-gray-800 dark:border-gray-700">
        <div class="flex flex-col justify-center h-full">
          <!-- Button Container for Timeframe -->
          <div class="flex space-x-2 mb-4">
            <button v-for="option in timeframeOptions" :key="option" @click="selectedTimeframe = option" :class="[
              'text-xs bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded',
              selectedTimeframe === option ? 'bg-blue-500 text-white' : ''
            ]">
              {{ option }}
            </button>
          </div>
          <!-- Chart Container -->
          <div class="flex items-center justify-center h-full">
            <Line :data="chartData" :options="chartOptions" />
          </div>
        </div>
      </div>

      <!-- Card 4: Tab Navigation & Content -->
      <div
        class="lg:col-span-6 lg:row-span-1 w-full bg-white border border-gray-200 rounded-lg shadow sm:p-4 dark:bg-gray-800 dark:border-gray-700">
        <!-- Navigation Tabs -->
        <div class="text-sm font-medium text-gray-500 border-b border-gray-200 dark:text-gray-400 dark:border-gray-700">
          <ul class="flex flex-wrap -mb-px justify-start">
            <li class="me-2" v-for="tab in tabs" :key="tab.id">
              <a href="#" @click.prevent="activeTab = tab.id" :class="[
                'inline-block p-4 border-b-2 rounded-t-lg hover:text-gray-600 hover:border-gray-300 dark:hover:text-gray-300',
                activeTab === tab.id ? 'text-blue-600 border-blue-600' : 'border-transparent'
              ]">
                {{ tab.name }}
              </a>
            </li>
          </ul>
        </div>
        <!-- Content Section for คำแนะนำ -->
        <div v-if="activeTab === 'tab1'" class="p-4">
          <!-- Icon Section -->
          <div class="flex justify-center items-center space-x-12">
            <!-- Icon for ซื้อ -->
            <div class="flex flex-col items-center">
              <svg xmlns="http://www.w3.org/2000/svg" class="w-16 h-16 text-green-500" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
              </svg>
              <span class="mt-2 text-xl font-bold">ซื้อ</span>
            </div>
            <!-- Icon for ไม่ซื้อ -->
            <div class="flex flex-col items-center">
              <svg xmlns="http://www.w3.org/2000/svg" class="w-16 h-16 text-red-500" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
              </svg>
              <span class="mt-2 text-xl font-bold">ไม่ซื้อ</span>
            </div>
          </div>
        </div>
        <!-- Content Section for รายละเอียดกองทุน -->
        <div v-if="activeTab === 'tab2'" class="p-4">
          <div class="relative grid grid-cols-1 md:grid-cols-2 gap-16">
            <div>
              <!-- หนังสือชี้ชวน -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">หนังสือชี้ชวน</span>
                <span class="text-gray-500 dark:text-gray-400">หนังสือชี้ชวน</span>
              </div>
              <!-- บลจ. -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">บลจ.</span>
                <span class="text-gray-500 dark:text-gray-400">ชื่อ บลจ.</span>
              </div>
              <!-- ประเภทกองทุน -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">ประเภทกองทุน</span>
                <span class="text-gray-500 dark:text-gray-400">หุ้น</span>
              </div>
              <!-- ความเสี่ยง -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">ความเสี่ยง</span>
                <span class="text-gray-500 dark:text-gray-400">หุ้น</span>
              </div>
              <!-- จ่ายปันผล -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">นโยบายการจ่ายปันผล</span>
                <span class="text-gray-500 dark:text-gray-400">ม่ายจ่าย</span>
              </div>
              <!-- รายการที่ 2 -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">มูลค่าทรัพย์สินสุทธิ</span>
                <span class="text-gray-500 dark:text-gray-400">1,000,000 บาท</span>
              </div>
              <div class="hidden md:block absolute inset-y-0 left-1/2 w-px bg-gray-300"></div>
            </div>
            <div>
              <!-- ค่าธรรมเนียมการขาย -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">ค่าธรรมเนียมการขาย</span>
                <span class="text-gray-500 dark:text-gray-400">0.00%</span>
              </div>
              <!-- ค่าธรรมเนียมรับซื้อคืน -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">ค่าธรรมเนียมรับซื้อคืน</span>
                <span class="text-gray-500 dark:text-gray-400">0.00%</span>
              </div>
              <!-- ค่าใช้จ่ายกองทุนรวม -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">ค่าใช้จ่ายกองทุนรวม</span>
                <span class="text-gray-500 dark:text-gray-400">0.00%</span>
              </div>
              <!-- ลงทุนขั้นต่ำ -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">ลงทุนขั้นต่ำ</span>
                <span class="text-gray-500 dark:text-gray-400">0.00%</span>
              </div>
              <!-- วันจดทะเบียนกองทุน -->
              <div class="flex justify-between items-center mb-2">
                <span class="font-semibold">วันจดทะเบียนกองทุน</span>
                <span class="text-gray-500 dark:text-gray-400">0.00%</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "@/components/Navbar";
import BuysellPopUp from "@/components/BuysellPopUp.vue";
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend
} from "chart.js";
import { Line } from "vue-chartjs";
import { ref, onMounted } from 'vue';

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend);

export default {
  name: "Buysell",
  components: {
    Navbar,
    Line,
    BuysellPopUp
  },

  setup() {
    const SummaryData = ref([
      { duration: '3 เดือน', percentage: '-10%' },
      { duration: '6 เดือน', percentage: '-10%' },
      { duration: 'YTD', percentage: '-10%' },
      { duration: '3 ปี', percentage: '-10%' },
      { duration: '5 ปี', percentage: '-10%' },
      { duration: '10 ปี', percentage: '-10%' },
    ])

    const selectedContent = ref('return')

    /* Use stage data for "ผลการดำเนินการย้อนหลัง" */

    const updateContent = async (content) => {

      var data

      /* ทุกครั้งที่เปลี่ยนแถบ update เลย */
      const jwtToken = localStorage.getItem("jwtToken") // ✅ ดึง Token จาก localStorage
      if (!jwtToken) {
        console.error("JWT token ไม่ถูกพบใน localStorage")
        return
      }

      try {
        const response = await fetch("/api/performance-mutual-funds/lastest/BERMF",
        // const response = await fetch(`/api/performance-mutual-funds/lastest/${this.$route.query.name || ""}`,
          {
            method: "GET",
            headers: {
              "Authorization": `Bearer ${jwtToken}`, // ✅ ใช้ Token ที่ดึงมา
              "Content-Type": "application/json"
            }
          }
        )

        data = await response.json()

        console.log("get_performance_mutual_funds :", data)


      } catch (error) {
        console.error("Error in get_performance_mutual_funds :", error)
      }


      selectedContent.value = content
      if (content === 'return') {
        // เปลี่ยนข้อมูลใน SummaryData สำหรับ 'return'
        SummaryData.value = [
          { duration: '3 เดือน', percentage: data['tree_month_roc'] || '-' },
          { duration: '6 เดือน', percentage: data['six_month_roc'] || '-'  },
          { duration: 'YTD', percentage: data['ytd_roc'] || '-' },
          { duration: '3 ปี', percentage: data['six_month_roc'] || '-' },
          { duration: '5 ปี', percentage: data['five_year_roc'] || '-' },
          { duration: '10 ปี', percentage: data['ten_year_roc'] || '-' },
        ];
      } else if (content === 'sd') {
        // เปลี่ยนข้อมูลใน SummaryData สำหรับ 'sd'
        SummaryData.value = [
          { duration: '3 เดือน', percentage: data['tree_month_roc'] || '-' },
          { duration: '6 เดือน', percentage: data['six_month_roc'] || '-' },
          { duration: 'YTD', percentage: data['ytd_std'] || '-'  },
          { duration: '3 ปี', percentage: data['std_three_year'] || '-'  },
          { duration: '5 ปี', percentage: data['std_five_year'] || '-' },
          { duration: '10 ปี', percentage: data['std_ten_year'] || '-' },
        ];
      } else if (content === 'sharpeRatio') {
        // เปลี่ยนข้อมูลใน SummaryData สำหรับ 'sharpeRatio'
        SummaryData.value = [
          { duration: '3 เดือน', percentage: data['sharpe_ratio_three_month'] || '-' },
          { duration: '6 เดือน', percentage: data['sharpe_ratio_six_month'] || '-' },
          { duration: 'YTD', percentage: data['sharpe_ratio_one_year'] || '-' },
          { duration: '3 ปี', percentage: data['sharpe_ratio_three_year'] || '-' },
          { duration: '5 ปี', percentage: data['sharpe_ratio_five_year'] || '-' },
          { duration: '10 ปี', percentage: data['sharpe_ratio_ten_year'] || '-' },
        ];
      }

      console.log(selectedContent.value) /* <--- Debug selectedContent */
    }

    onMounted(() => {
      updateContent()
    })


    return {
      SummaryData,
      updateContent,
    };
  },

  data() {
    return {
      apiData: [],
      showMenu: false,
      chartData: {
        labels: [], // chartData will be populated via fetchData or computed if needed
        datasets: [
          {
            label: "Data One",
            backgroundColor: "rgba(75,192,192,0.2)",
            borderColor: "rgba(75,192,192,1)",
            pointBackgroundColor: "rgba(75,192,192,1)",
            data: [] // Data array will be set after fetching data
          }
        ]
      },
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false
      },
      activeTab: "tab1", // กำหนดแท็บเริ่มต้นที่แสดง
      tabs: [
        { id: "tab1", name: "คำแนะนำ" },
        { id: "tab2", name: "รายละเอียดกองทุน" }
      ],
    };
  },
  computed: {
    // รับชื่อกองทุนจาก query parameter ใน route
    fundName() {
      return this.$route.query.name || "";
    }
  },
  methods: {
    toggleMenu() {
      this.showMenu = !this.showMenu;
    },
    handleClickOutside(event) {
      if (this.$refs.menuContainer && !this.$refs.menuContainer.contains(event.target)) {
        this.showMenu = false;
      }
    },
    openModal() {
      this.showMenu = false;
      if (this.$refs.buySellModal && this.$refs.buySellModal.openModal) {
        this.$refs.buySellModal.openModal();
      }
    },
    async fetchData() {
      try {
        // ใช้ fundName จาก route ใน URL สำหรับ fetch ข้อมูล
        const endpoint = `/api/nav-history/fund/${encodeURIComponent(this.fundName)}`;
        const response = await fetch(endpoint, {
          method: "GET",
          headers: {
            "Content-Type": "application/json"
          }
        });
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        console.log("Fetched API data:", data);
        this.apiData = data;
        // หากต้องการอัพเดท chartData จาก apiData ให้คุณอัพเดท chartData ด้วย
        this.updateChartData();
      } catch (error) {
        console.error("Failed to fetch API data:", error);
      }
    },
    updateChartData() {
      // สมมุติว่าแต่ละ object ใน apiData มี field "date" และ "nav"
      const labels = this.apiData.map(item => item.date);
      const dataPoints = this.apiData.map(item => item.nav);
      this.chartData = {
        labels,
        datasets: [
          {
            label: "NAV",
            data: dataPoints,
            fill: false,
            borderColor: "rgba(75,192,192,1)",
            backgroundColor: "rgba(75,192,192,0.2)",
            tension: 0.1
          }
        ]
      };
    },
  },

  mounted() {
    this.fetchData();
    document.addEventListener("click", this.handleClickOutside);
  },
  beforeUnmount() {
    document.removeEventListener("click", this.handleClickOutside);
  },
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
</style>
