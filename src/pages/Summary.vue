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
                <input type="checkbox" 
                       v-model="selectedFundTypes" 
                       :value="type" 
                       class="form-checkbox rounded border-gray-300">
                <span class="text-sm">{{ type }}</span>
              </label>
            </div>
          </div>
  
          <!-- Tax Saving Fund Radio -->
          <div class="flex flex-col space-y-2">
            <label class="text-sm font-medium">กองทุนลดหย่อนภาษี:</label>
            <div class="flex gap-4">
              <label v-for="type in taxSavingTypes" :key="type" class="flex items-center space-x-2">
                <input type="radio" 
                       v-model="selectedTaxSaving" 
                       :value="type" 
                       class="form-radio border-gray-300">
                <span class="text-sm">{{ type }}</span>
              </label>
            </div>
          </div>
        </div>
  
        <!-- Table Section -->
        <table class="w-full bg-white border rounded-lg shadow-md overflow-hidden">
          <thead>
            <tr class="bg-green-500 text-white">
              <th v-for="column in columns" 
                  :key="column.key" 
                  class="py-2 px-4 text-sm">
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
            <tr v-for="fund in paginatedFunds" 
                :key="fund.fundName" 
                class="odd:bg-gray-100 even:bg-white hover:bg-gray-300">
              <td class="py-2 px-4 text-center">{{ fund.fundName }}</td>
              <td class="py-2 px-4 text-center">{{ fund.gl }}</td>
              <td class="py-2 px-4 text-center">{{ fund.currentValue }}</td>
              <td class="py-2 px-4 text-center">{{ fund.holdingUnits }}</td>
              <td class="py-2 px-4 text-center">{{ fund.cost }}</td>
              <td class="py-2 px-4 text-center">{{ fund.glPercent }}</td>
              <td class="py-2 px-4 text-center">{{ fund.currentInvestment }}</td>
              <td class="py-2 px-4 text-center">{{ fund.averageInvestment }}</td>
              <td class="py-2 px-4 text-center">{{ fund.sellableUnits }}</td>
            </tr>
          </tbody>
        </table>
  
        <!-- Pagination -->
        <div class="flex justify-around items-center mt-4">
          <button
            @click="prevPage"
            :disabled="currentPage === 1"
            class="px-4 py-2 bg-orange-400 hover:bg-orange-500 text-white rounded-md disabled:opacity-50 disabled:cursor-not-allowed">
            🡄 ย้อนกลับ
          </button>
          <span class="text-white">หน้า {{ currentPage }}</span>
          <button
            @click="nextPage"
            :disabled="currentPage === totalPages"
            class="px-4 py-2 bg-orange-400 hover:bg-orange-500 text-white rounded-md disabled:opacity-50 disabled:cursor-not-allowed">
            ต่อไป 🡆
          </button>
        </div>
      </main>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, watch } from 'vue'
  import Navbar from '@/components/Navbar.vue'
  
  // Keep columns definition unchanged - type won't be shown
  const columns = [
    { key: 'fundName', label: 'ชื่อกองทุน' },
    { key: 'gl', label: 'GL' },
    { key: 'currentValue', label: 'มูลค่าปัจจุบัน' },
    { key: 'holdingUnits', label: 'จำนวนที่ถือ' },
    { key: 'cost', label: 'ต้นทุน' }, 
    { key: 'glPercent', label: '%GL' },
    { key: 'currentInvestment', label: 'ลงทุนปัจจุบัน' },
    { key: 'averageInvestment', label: 'ลงทุนเฉลี่ย' },
    { key: 'sellableUnits', label: 'หน่วยที่ขายได้' }
  ]
  
  // Update mock data structure to include type field
  const funds = ref([
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้น',
      gl: 1550,
      currentValue: 1150,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตลาดเงินในประเทศ',
      taxSaving: 'ThaiESG'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'RMF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
    {
      fundName: 'กองทุนเปิดบัวหลวงหุ้นระยะยาว',
      gl: 1500,
      currentValue: 11500,
      holdingUnits: 1000,
      cost: 10000,
      glPercent: '15%',
      currentInvestment: 11500, 
      averageInvestment: 10000,
      sellableUnits: 1000,
      fundTypes: 'กองทุนรวมตราสารทุน',
      taxSaving: 'SSF'
    },
  ])
  
  const currentPage = ref(1)
  const perPage = ref(10) // Set items per page to 10
  const sortKey = ref('fundName')
  const sortOrder = ref(1)
  
  const today = new Date().toISOString().split('T')[0]
  const dateRange = ref({
    start: null,
    end: null
  })
  
  const formatDate = (date) => {
    return new Date(date).toLocaleDateString('th-TH', {
      year: 'numeric',
      month: 'long',
      day: 'numeric'
    })
  }
  
  const fundTypes = ref([
    'กองทุนรวมตลาดเงินในประเทศ',
    'กองทุนรวมตลาดเงินต่างประเทศ',
    'กองทุนรวมตราสารหนี้',
    'กองทุนรวมผสม', 
    'กองทุนรวมตราสารทุน',
    'กองทุนรวมหมวดอุตสาหกรรม',
    'กองทุนรวมสินทรัพย์ทางเลือก'
  ])
  
  const taxSavingTypes = ref(['SSF', 'RMF', 'ThaiESG'])
  const selectedFundTypes = ref([])
  const selectedTaxSaving = ref('ทั้งหมด')
  
  // Add computed property for filtered funds
  const filteredFunds = computed(() => {
    return funds.value.filter(fund => {
      // Filter by selected fund types
      const matchesFundType = selectedFundTypes.value.length === 0 || 
                             selectedFundTypes.value.includes(fund.fundTypes)
  
      // Filter by tax saving type                       
      const matchesTaxSaving = selectedTaxSaving.value === 'ทั้งหมด' || 
                              fund.taxSaving === selectedTaxSaving.value
  
      return matchesFundType && matchesTaxSaving
    })
  })
  
  // Update filtered data computed property
  const filteredData = computed(() => {
    let result = funds.value
  
    // กรองตามประเภทกองทุน
    if (!selectedFundTypes.value.includes('ทั้งหมด')) {
      result = result.filter(fund => 
        selectedFundTypes.value.includes(fund.fundTypes)
      )
    }
  
    // กรองตามประเภทกองทุนลดหย่อนภาษี
    if (selectedTaxSaving.value !== 'ทั้งหมด') {
      result = result.filter(fund => fund.fundTypes === selectedTaxSaving.value)
    }
  
    // กรองตามช่วงวันที่เฉพาะเมื่อมีการเลือกทั้ง start และ end
    if (dateRange.value.start && dateRange.value.end) {
      const startDate = new Date(dateRange.value.start)
      const endDate = new Date(dateRange.value.end)
      startDate.setHours(0, 0, 0, 0)
      endDate.setHours(23, 59, 59, 999)
      
      result = result.filter(fund => {
        const fundDate = new Date(fund.date)
        return fundDate >= startDate && fundDate <= endDate
      })
    }
  
    return result
  })
  
  const filteredAndSortedFunds = computed(() => {
    // กรองข้อมูลก่อน
    let result = funds.value.filter(fund => {
      const matchesFundType = selectedFundTypes.value.length === 0 || 
                             selectedFundTypes.value.includes(fund.fundTypes)
      const matchesTaxSaving = selectedTaxSaving.value === 'ทั้งหมด' || 
                              fund.taxSaving === selectedTaxSaving.value
      return matchesFundType && matchesTaxSaving
    })
  
    // จัดเรียงข้อมูล
    return result.sort((a, b) => {
      let result = 0
      if (a[sortKey.value] > b[sortKey.value]) result = 1
      if (a[sortKey.value] < b[sortKey.value]) result = -1
      return result * sortOrder.value
    })
  })
  
  // ปรับปรุง paginatedFunds ให้ใช้ filteredAndSortedFunds
  const paginatedFunds = computed(() => {
    const start = (currentPage.value - 1) * perPage.value
    const end = start + perPage.value
    return filteredAndSortedFunds.value.slice(start, end)
  })
  
  // ปรับปรุง totalPages ให้ใช้ filteredAndSortedFunds
  const totalPages = computed(() => 
    Math.ceil(filteredAndSortedFunds.value.length / perPage.value)
  )
  
  // ปรับปรุงฟังก์ชัน sort
  const sort = (key) => {
    if (sortKey.value === key) {
      sortOrder.value *= -1
    } else {
      sortKey.value = key
      sortOrder.value = 1
    }
    // รีเซ็ตหน้าเมื่อมีการเรียงลำดับใหม่
    currentPage.value = 1
  }
  
  const prevPage = () => {
      if (currentPage.value > 1) currentPage.value--
  }
  
  const nextPage = () => {
      if (currentPage.value < totalPages.value) currentPage.value++
  }
  
  // Reset dateRange function
  const resetDateRange = () => {
    dateRange.value = {
      start: null,
      end: null
    }
  }
  
  watch([selectedFundTypes, selectedTaxSaving, dateRange], () => {
    currentPage.value = 1 // Reset to first page when filters change
  }, { deep: true })
  
  watch([dateRange], () => {
    if (dateRange.value.start && dateRange.value.end) {
      currentPage.value = 1
    }
  }, { deep: true })
  
  watch([selectedTaxSaving], () => {
    currentPage.value = 1 // Reset pagination เมื่อเปลี่ยน filter
  })
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