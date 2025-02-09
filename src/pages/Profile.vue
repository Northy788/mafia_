<template>
    <div class="min-h-screen">
        <Navbar />
        <div class=" flex justify-center">

            <!-- ✅ Main Container -->
            <div class="w-full max-w-6xl px-6 py-4">
                <div class="bg-white rounded-lg shadow-lg overflow-hidden">
                    <!-- ✅ Tabs -->
                    <div class="flex border-b">
                        <button @click="activeTab = 'profile'"
                            :class="['px-6 py-3 flex-1 text-center', activeTab === 'profile' ? 'border-b-2 border-green-500 text-green-500 font-bold' : 'text-gray-500']">
                            ข้อมูลผู้ใช้
                        </button>
                        <button @click="activeTab = 'tax'"
                            :class="['px-6 py-3 flex-1 text-center', activeTab === 'tax' ? 'border-b-2 border-green-500 text-green-500 font-bold' : 'text-gray-500']">
                            ข้อมูลภาษี
                        </button>
                    </div>

                    <!-- ✅ Profile Section -->
                    <div v-if="activeTab === 'profile'" class="p-6">
                        <div class="flex items-center justify-between">
                            <div class="flex items-center space-x-4">
                                <img :src="profile.image" alt="Profile Image"
                                    class="w-14 h-14 rounded-full border border-gray-300" />
                                <div>
                                    <h2 class="text-lg font-semibold">John Doe</h2>
                                    <p class="text-gray-500">tesr@hotmail.com</p>
                                </div>
                            </div>
                            <button v-if="!isEditing"
                                class="text-white px-4 py-2 rounded-lg bg-green-500 hover:bg-green-600 font-bold">
                                ✎ แก้ไขโปรไฟล์
                            </button>
                        </div>

                        <!-- Editable Profile Form -->
                        <div class="mt-6 grid grid-cols-2 gap-4">
                            <!-- ✅ ชื่อ-นามสกุล -->
                            <div>
                                <label class="text-gray-600 font-bold">ชื่อ-นามสกุล</label>
                                <input type="text" class="input-field" :disabled="!isEditing" />
                            </div>

                            <!-- ✅ อีเมล -->
                            <div>
                                <label class="text-gray-600 font-bold">อีเมล</label>
                                <input type="text" class="input-field" :disabled="!isEditing" />
                            </div>

                            <!-- ✅ เบอร์โทรศัพท์ -->
                            <div>
                                <label class="text-gray-600 font-bold">เบอร์โทรศัพท์</label>
                                <input type="text" class="input-field" :disabled="!isEditing" />
                            </div>

                            <!-- ✅ วันเกิด -->
                            <div>
                                <label class="text-gray-600 font-bold">วันเกิด</label>
                                <input type="date" class="input-field" :disabled="!isEditing" />
                            </div>

                            <!-- ✅ อาชีพ -->
                            <div>
                                <label class="text-gray-600 font-bold">อาชีพ</label>
                                <input type="text" class="input-field" :disabled="!isEditing" />
                            </div>

                            <!-- ✅ เงินเดือน -->
                            <div>
                                <label class="text-gray-600 font-bold">เงินเดือน</label>
                                <input type="number" class="input-field" :disabled="!isEditing" />
                            </div>

                            <!-- ✅ ระดับความเสี่ยง -->
                            <div>
                                <label class="text-gray-600 font-bold">ระดับความเสี่ยง</label>
                                <select class="input-field" :disabled="!isEditing">
                                    <option value="ต่ำ">ต่ำ</option>
                                    <option value="ปานกลาง">ปานกลาง</option>
                                    <option value="สูง">สูง</option>
                                </select>
                            </div>
                        </div>

                        <!-- Buttons -->
                        <div class="mt-6 text-center flex space-x-4 justify-center">
                            <button v-if="isEditing"
                                class="bg-green-500 text-white px-6 py-2 rounded-lg hover:bg-green-600 font-bold">
                                ✔ บันทึกข้อมูล
                            </button>
                            <button v-else
                                class="bg-red-500 text-white px-6 py-2 rounded-lg hover:bg-red-600 font-bold">
                                ❌ ออกจากระบบ
                            </button>
                        </div>
                    </div>

                    <!-- ✅ Tax Information Section -->
                    <div v-if="activeTab === 'tax'" class="p-6">
                        <div class="grid grid-cols-1 gap-6">
                            <!-- 🔹 Income Section -->
                            <div class="bg-white rounded-lg p-4 shadow-md">
                                <h3 class="text-lg font-bold mb-4 text-gray-900">💰 รายได้ & หักค่าใช้จ่าย</h3>
                                <div class="grid grid-cols-2 gap-4">
                                    <div>
                                        <label class="text-gray-600 font-bold">รายได้ต่อเดือน</label>
                                        <input type="number" class="input-field" placeholder="50,000" />
                                    </div>
                                    <div>
                                        <label class="text-gray-600 font-bold">หักค่าใช้จ่าย (สูงสุด 50%)</label>
                                        <p class="output-field">25,000</p>
                                    </div>
                                    <div>
                                        <label class="text-gray-600 font-bold">โบนัส</label>
                                        <input type="number" class="input-field" placeholder="100,000" />
                                    </div>
                                    <div>
                                        <label class="text-gray-600 font-bold">หักค่าใช้จ่าย (สูงสุด 50%)</label>
                                        <p class="output-field">50,000</p>
                                    </div>
                                    <div>
                                        <label class="text-gray-600 font-bold">รายได้อื่น ๆ</label>
                                        <input type="number" class="input-field" placeholder="20,000" />
                                    </div>
                                    <div>
                                        <label class="text-gray-600 font-bold">หักค่าใช้จ่าย (สูงสุด 50%)</label>
                                        <p class="output-field">10,000</p>
                                    </div>
                                </div>
                            </div>

                            <!-- 🔹 Family Tax Deductions -->
                            <div class="bg-white rounded-lg p-4 shadow-md">
                                <h3 class="text-lg font-bold mb-4 text-gray-900">👨‍👩‍👧‍👦 รายการลดหย่อนภาษี: ครอบครัว
                                </h3>
                                <div class="grid grid-cols-2 gap-4">
                                    <!-- ✅ สถานะสมรส -->
                                    <div>
                                        <label class="text-gray-600 font-bold">สถานะสมรส</label>
                                        <select class="input-field">
                                            <option value="single">โสด</option>
                                            <option value="married">สมรส</option>
                                            <option value="divorced">หย่า</option>
                                        </select>
                                    </div>
                                    <!-- ✅ ลดหย่อนภาษีส่วนบุคคลและคู่สมรส (อยู่ในแถวเดียวกัน) -->
                                    <div class="grid grid-cols-2 gap-4">
                                        <!-- ✅ ลดหย่อนส่วนบุคคล -->
                                        <div>
                                            <label class="text-gray-600 font-bold">ลดหย่อนส่วนบุคคล</label>
                                            <p class="output-field">60,000 บาท</p>
                                        </div>

                                        <!-- ✅ ลดหย่อนคู่สมรส -->
                                        <div>
                                            <label class="text-gray-600 font-bold">ลดหย่อนคู่สมรส</label>
                                            <p class="output-field">60,000 บาท</p>
                                        </div>
                                    </div>


                                    <!-- ✅ จำนวนบุตร (ทั้งหมดในแถวเดียว) -->
                                    <div class="grid grid-cols-3 gap-4">
                                        <!-- ✅ จำนวนบุตร -->
                                        <div>
                                            <label class="text-gray-600 font-bold">จำนวนบุตร</label>
                                            <input type="number" class="input-field" placeholder="0" />
                                        </div>

                                        <!-- ✅ จำนวนบุตรที่เกิดหลังปี 2567 -->
                                        <div>
                                            <label class="text-gray-600 font-bold">บุตรเกิดหลังปี 2567</label>
                                            <input type="number" class="input-field" placeholder="0" />
                                        </div>

                                        <!-- ✅ จำนวนบุตรบุญธรรม -->
                                        <div>
                                            <label class="text-gray-600 font-bold">จำนวนบุตรบุญธรรม</label>
                                            <input type="number" class="input-field" placeholder="0" />
                                        </div>
                                    </div>

                                    <div>
                                        <label class="text-gray-600 font-bold">ลดหย่อนภาษีจากบุตร</label>
                                        <p class="output-field">30,000 ต่อคน</p>
                                    </div>



                                    <!-- ✅ จำนวนพ่อแม่ที่ดูแล -->
                                    <div>
                                        <label class="text-gray-600 font-bold">จำนวนพ่อแม่ที่ดูแล</label>
                                        <input type="number" class="input-field" placeholder="0" />
                                    </div>
                                    <div>
                                        <label class="text-gray-600 font-bold">ลดหย่อนภาษีจากพ่อแม่</label>
                                        <p class="output-field">30,000 ต่อคน</p>
                                    </div>

                                    <!-- ✅ จำนวนผู้พิการที่ดูแล -->
                                    <div>
                                        <label class="text-gray-600 font-bold">จำนวนผู้พิการที่ดูแล</label>
                                        <input type="number" class="input-field" placeholder="0" />
                                    </div>
                                    <div>
                                        <label class="text-gray-600 font-bold">ลดหย่อนภาษีจากผู้พิการ</label>
                                        <p class="output-field">60,000 ต่อคน</p>
                                    </div>
                                </div>
                            </div>


                            <!-- 🔹 Tax Deductions: Insurance & Social Benefits -->
                            <div class="bg-white rounded-lg p-4 shadow-md">
                                <h3 class="text-lg font-bold mb-4 text-gray-900">🏥 รายการลดหย่อนภาษี:
                                    ประกันและสวัสดิการ</h3>
                                <div class="grid grid-cols-2 gap-4">
                                    <!-- ✅ ประกันสังคม -->
                                    <div>
                                        <label class="text-gray-600 font-bold">เงินสมทบประกันสังคม</label>
                                        <input type="number" class="input-field" placeholder="9,000" />
                                    </div>

                                    <!-- ✅ ประกันชีวิต -->
                                    <div>
                                        <label class="text-gray-600 font-bold">เบี้ยประกันชีวิต</label>
                                        <input type="number" class="input-field" placeholder="100,000" />
                                    </div>

                                    <!-- ✅ ประกันสุขภาพ -->
                                    <div>
                                        <label class="text-gray-600 font-bold">เบี้ยประกันสุขภาพ</label>
                                        <input type="number" class="input-field" placeholder="25,000" />
                                    </div>

                                    <!-- ✅ ประกันสุขภาพพ่อแม่ -->
                                    <div>
                                        <label class="text-gray-600 font-bold">เบี้ยประกันสุขภาพพ่อแม่</label>
                                        <input type="number" class="input-field" placeholder="15,000" />
                                    </div>
                                    <!-- ✅ ประกันบำนาญ -->
                                    <div>
                                        <label class="text-gray-600 font-bold">เบี้ยประกันบำนาญ</label>
                                        <input type="number" class="input-field" placeholder="200,000" />
                                    </div>
                                </div>
                            </div>

                            <!-- 🔹 Tax Deductions: Donations -->
                            <div class="bg-white rounded-lg p-4 shadow-md">
                                <h3 class="text-lg font-bold mb-4 text-gray-900">🎗 ลดหย่อนภาษี: เงินบริจาค</h3>
                                <div class="grid grid-cols-2 gap-4">
                                    <!-- ✅ บริจาคทั่วไป -->
                                    <div>
                                        <label class="text-gray-600 font-bold">เงินบริจาคทั่วไป</label>
                                        <input type="number" class="input-field" placeholder="50,000" />
                                    </div>

                                    <!-- ✅ บริจาคเพื่อการศึกษา -->
                                    <div>
                                        <label class="text-gray-600 font-bold">เงินบริจาคเพื่อการศึกษา</label>
                                        <input type="number" class="input-field" placeholder="100,000" />
                                    </div>

                                    <!-- ✅ บริจาคเพื่อการเมือง -->
                                    <div>
                                        <label class="text-gray-600 font-bold">เงินบริจาคเพื่อการเมือง</label>
                                        <input type="number" class="input-field" placeholder="10,000" />
                                    </div>
                                </div>
                            </div>


                            <!-- 🔹 รายการลดหย่อนภาษี: อื่น ๆ -->
                            <div class="bg-white rounded-lg p-4 shadow-md">
                                <h3 class="text-lg font-bold mb-4 text-gray-900">📝 รายการลดหย่อนภาษี: อื่น ๆ</h3>
                                <div class="grid grid-cols-2 gap-4">
                                    <!-- ✅ ช้อปดีมีคืน (Easy Receipt) -->
                                    <div>
                                        <label class="text-gray-600 font-bold">ช้อปดีมีคืน (Easy Receipt)</label>
                                        <input type="number" class="input-field" placeholder="50000" />
                                    </div>

                                    <!-- ✅ ท่องเที่ยวเมืองรอง -->
                                    <div>
                                        <label class="text-gray-600 font-bold">ท่องเที่ยวเมืองรอง</label>
                                        <input type="number" class="input-field" placeholder="15000" />
                                    </div>

                                    <!-- ✅ ดอกเบี้ยบ้าน -->
                                    <div>
                                        <label class="text-gray-600 font-bold">ดอกเบี้ยสินเชื่อบ้าน</label>
                                        <input type="number" class="input-field" placeholder="100000" />
                                    </div>

                                    <!-- ✅ ซื้อบ้านใหม่ -->
                                    <div>
                                        <label class="text-gray-600 font-bold">ค่าซื้อบ้านใหม่</label>
                                        <input type="number" class="input-field" placeholder="200000" />
                                    </div>

                                    <!-- ✅ ค่าฝากครรภ์และคลอดบุตร -->
                                    <div>
                                        <label class="text-gray-600 font-bold">ค่าฝากครรภ์และคลอดบุตร</label>
                                        <input type="number" class="input-field" placeholder="60000" />
                                    </div>
                                </div>
                            </div>
                            <!-- 🔹 การลงทุนลดหย่อนภาษี -->
                            <div class="bg-white rounded-lg p-4 shadow-md">
                                <h3 class="text-lg font-bold mb-4 text-gray-900">📈 การลงทุนลดหย่อนภาษี</h3>
                                <div class="grid grid-cols-2 gap-4">
                                    <!-- ✅ RMF -->
                                    <div>
                                        <label class="text-gray-600 font-bold">RMF</label>
                                        <input type="number" class="input-field" placeholder="50000" />
                                    </div>

                                    <!-- ✅ SSF -->
                                    <div>
                                        <label class="text-gray-600 font-bold">SSF</label>
                                        <input type="number" class="input-field" placeholder="30000" />
                                    </div>

                                    <!-- ✅ PVD -->
                                    <div>
                                        <label class="text-gray-600 font-bold">กองทุนสำรองเลี้ยงชีพ (PVD)</label>
                                        <input type="number" class="input-field" placeholder="20000" />
                                    </div>

                                    <!-- ✅ GPF -->
                                    <div>
                                        <label class="text-gray-600 font-bold">กองทุนบำเหน็จบำนาญข้าราชการ (GPF)</label>
                                        <input type="number" class="input-field" placeholder="15000" />
                                    </div>

                                    <!-- ✅ NSF -->
                                    <div>
                                        <label class="text-gray-600 font-bold">กองทุนการออมแห่งชาติ (NSF)</label>
                                        <input type="number" class="input-field" placeholder="10000" />
                                    </div>

                                    <!-- ✅ Thai ESG -->
                                    <div>
                                        <label class="text-gray-600 font-bold">Thai ESG</label>
                                        <input type="number" class="input-field" placeholder="25000" />
                                    </div>
                                </div>
                            </div>

                        </div>

                        <!-- 🔹 Save Tax Info Button -->
                        <div class="text-center mt-6">
                            <button @click="saveTaxInfo"
                                class="bg-green-500 text-white px-6 py-2 rounded-lg hover:bg-green-600 font-bold">
                                ✔ บันทึกข้อมูล
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>


<script>
import Navbar from '@/components/Navbar.vue';

export default {
    components: { Navbar },
    data() {
        return {
            activeTab: 'profile',
            isEditing: false,
            profile: {
                name: 'Usertest Attendance',
                email: 'user_test@gmail.com',
                image: require('@/assets/icon/profile.png')
            },
            taxInfo: {
                monthlyIncome: 0,
                bonus: 0,
                otherIncome: 0,
                maritalStatus: 'โสด',
                numChildren: 0,
                rmf: 0,
                ssf: 0
            }
        };
    },
    methods: {
        toggleEdit() {
            this.isEditing = true;
        },
        saveProfile() {
            alert('บันทึกข้อมูลโปรไฟล์เรียบร้อย!');
            this.isEditing = false;
        },
        saveTaxInfo() {
            alert('บันทึกข้อมูลภาษีเรียบร้อย!');
        }
    }
};
</script>

<style scoped>
.input-field {
    width: 100%;
    border: 1px solid #ccc;
    padding: 6px;
    border-radius: 6px;
    transition: border 0.2s ease-in-out;
}

.input-field:focus {
    border-color: #22c55e;
    outline: none;
}

.output-field {
    width: 100%;
    padding: 6px;
    background-color: #f3f4f6;
    border-radius: 6px;
    text-align: right;
    font-weight: bold;
    color: #374151;
}
</style>
