<template>
  <div class="min-h-screen bg-gray-100">
    <!-- นำ Navbar มาแสดง -->
    <Navbar />
    <main class="container py-3 px-10">
      <h1 class="text-2xl font-bold mb-6 text-white">แนะนำกองทุนแบบจัดอันดับ</h1>
      <div class="mb-4 relative">
        <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
          <img src="@/assets/icon/magnifying-glass.png" alt="Search Icon" class="h-6 w-6" />
        </div>
        <input
          type="text"
          placeholder="ค้นหากองทุน"
          v-model="search"
          class="w-full p-2 pl-10 border rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-orange-400"
        />
      </div>

      <!-- แถวที่จะแสดงตารางข้อมูล -->
      <table class="w-full bg-white border rounded-lg shadow-md overflow-hidden">
        <thead>
          <tr class="bg-green-500 text-white">
            <th class="py-2 px-4 text-sm">
              <span class="cursor-pointer" @click="sort('fund_name')">
                ชื่อกองทุน
                <span v-if="sortKey === 'fund_name'">
                  {{ sortOrder === 1 ? '▲' : '▼' }}
                </span>
                <span v-else>▼</span>
              </span>
            </th>
            <th class="py-2 px-4 text-sm">
              <span class="cursor-pointer" @click="sort('fund_risk')">
                ระดับความเสี่ยง
                <span v-if="sortKey === 'fund_risk'">
                  {{ sortOrder === 1 ? '▲' : '▼' }}
                </span>
                <span v-else>▼</span>
              </span>
            </th>
            <th class="py-2 px-4 text-sm">1 ปี</th>
            <th class="py-2 px-4 text-sm">5 ปี</th>
            <th class="py-2 px-4 text-sm">10 ปี</th>
            <th class="py-2 px-4 text-sm">ส่วนเบี่ยงเบนมาตรฐาน</th>
            <th class="py-2 px-4 text-sm">Sharp Ratio</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item, index) in sortedData"
            :key="index"
            class="odd:bg-gray-100 even:bg-white hover:bg-gray-300"
          >
            <td
              @click="goToBuySell(item.fund_name)"
              class="py-2 px-4 text-center hover:underline cursor-pointer hover:text-green-500"
            >
              {{ item.fund_name }}
            </td>
            <td class="py-2 px-4 text-center">
              {{ item.fund_risk }}
            </td>
            <td class="py-2 px-4 text-center">temp</td>
            <td class="py-2 px-4 text-center">temp</td>
            <td class="py-2 px-4 text-center">temp</td>
            <td class="py-2 px-4 text-center">temp</td>
            <td class="py-2 px-4 text-center">temp</td>
          </tr>
        </tbody>
      </table>
    </main>
  </div>
</template>

<script>
import Navbar from "@/components/Navbar";
export default {
  name: "Home",
  components: {
    Navbar,
  },
  data() {
    return {
      apiData: [],
      search: "",
      // สำหรับ sort
      sortKey: "fund_name",
      sortOrder: 1,
      // หากต้องการ pagination สามารถเพิ่ม currentPage, perPage ได้
      // currentPage: 1,
      // perPage: 10,
    };
  },
  computed: {
    // กรองข้อมูลตาม search (ค้นหาใน fund_name)
    filteredData() {
      return this.apiData.filter((item) =>
        item.fund_name.toLowerCase().includes(this.search.toLowerCase())
      );
    },
    // เรียงข้อมูลตาม sortKey และ sortOrder
    sortedData() {
      return this.filteredData.slice().sort((a, b) => {
        let result = 0;
        if (a[this.sortKey] > b[this.sortKey]) result = 1;
        if (a[this.sortKey] < b[this.sortKey]) result = -1;
        return result * this.sortOrder;
      });
    },
    // หากต้องการใช้ pagination สามารถคำนวณข้อมูลในส่วนนี้ได้
    // paginatedData() {
    //   const start = (this.currentPage - 1) * this.perPage;
    //   const end = this.currentPage * this.perPage;
    //   return this.sortedData.slice(start, end);
    // }
  },
  methods: {
    sort(key) {
      if (this.sortKey === key) {
        this.sortOrder *= -1;
      } else {
        this.sortKey = key;
        this.sortOrder = 1;
      }
    },
    goToBuySell(name) {
      this.$router.push(`/buy-sell?name=${name}`);
    },
    async fetchData() {
      try {
        // Fetch ข้อมูล fund หลัก
        const response = await fetch("/api/mutual-fund/", {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
          },
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
  },
  mounted() {
    this.fetchData();
  },
};
</script>

<style>
.min-h-screen {
  background-image: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)), url('@/assets/images/bg1.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  position: relative;
}

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
