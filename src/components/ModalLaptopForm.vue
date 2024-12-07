<template>
  <div v-if="showModal" class="fixed top-0 left-0 z-50 flex items-center justify-center w-screen h-screen overflow-hidden">
    <div class="absolute top-0 left-0 w-full h-full bg-black/60" @click="handleCancel"></div>
    <div class="z-50 w-6/12 bg-white">
      <div>
        <div class="flex flex-row items-center justify-between px-8 py-8">
          <h2 class="text-xl">Tambah Data Laptop</h2>
          <span
            @click="handleCancel" 
            class="content-center w-8 h-8 text-center transition rounded-full cursor-pointer bg-slate-200 hover:bg-slate-300">X</span>
        </div>
        <div class="w-full border-b border-black"></div>
      </div>
      <div class="px-8 py-4">
        <form @submit.prevent="handleSubmit">
          <div class="h-[480px] pb-48 overflow-y-auto">

            <!-- Merk dan Tipe Laptop -->
            <div class="my-8">
              <InputText
                type="text"
                label="Merk dan Tipe Laptop"
                placeholder="Ex: Lenovo V14 G2"
                @bindValue="(e) => formData.laptopType = e "
                />
            </div>

            <!-- Harga -->
            <div class="my-8">
              <InputText
                type="text"
                label="Harga"
                placeholder="Rp."
                @bindValue="(e) => formData.price = parseInt(e) "
                />
            </div>
            
            <!-- Berat -->
            <div class="my-8">
              <InputText
                type="text"
                label="Berat"
                placeholder="Kg"
                @bindValue="(e) => formData.weight = parseInt(e) "
                />
            </div>
            
            <!-- Kapasitas ROM -->
            <div class="my-8">
              <InputSelect 
                :items="items.ROMCapacity" 
                label="Kapasitas ROM"
                placeholder="Ex: 8GB"
                @bindValue="(e) => formData.ROMCapacity = e "
              /> 
            </div>
            
            <!-- Kapasitas RAM -->
            <div class="my-8">
              <InputSelect 
                :items="items.RAMCapacity" 
                label="Kapasitas RAM"
                placeholder="Ex: 8GB"
                @bindValue="(e) => formData.RAMCapacity = e "
              /> 
            </div>
            
            <!-- Kecepatan RAM -->
            <div class="my-8">
              <InputSelect 
                :items="items.RAMSpeed" 
                label="Kecepatan RAM"
                placeholder="Ex: 3200Mhz"
                @bindValue="(e) => formData.RAMSpeed = e "
              /> 
            </div>
            
            <!-- Resolusi Layar -->
            <div class="my-8">
              <InputSelect 
                :items="items.resolution" 
                label="Resolusi Layar"
                placeholder="Ex: FHD"
                @bindValue="(e) => formData.resolution = e "
              /> 
            </div>

            <div class="my-8">
              <InputSelect
                :items="items.processor"
                label="Processor"
                placeholder="Ex: Intel Core i7-13100K"
                @bindValue="(e) => formData.processor = e " 
              />
            </div>
            
          </div>

          <!-- Buttons -->
          <div class="flex justify-end pt-4 space-x-4">
            <button
              type="button"
              @click="handleCancel"
              class="px-4 py-2 text-gray-700 bg-gray-200 rounded-md hover:bg-gray-300"
            >
              Cancel
            </button>
            <button
              type="submit"
              class="px-4 py-2 text-white bg-green-500 rounded-md hover:bg-green-600"
            >
              Simpan
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive } from 'vue';
import InputSelect from './forms/InputSelect.vue';
import InputText from './forms/InputText.vue';
import axios from 'axios';
const env = import.meta.env;

export default {
  props: {
    showModal: {
      type: Boolean,
      required: true
    }
  },
  data() {
    return {
      formData: reactive({
        laptopType: "",
        price: "",
        weight: "",
        processor: "",
        ROMCapacity: "",
        RAMCapacity: "",
        RAMSpeed: "",
        resolution: "",
        processor: ""
      }),
      processorType: "",
      processorGeneration: "",
      items: {
        ROMCapacity: [
        ],
        RAMCapacity: [
        ],
        RAMSpeed: [
        ],
        resolution: [
        ],
        processor: [
        ],
      }
    };
  },
  async created() {
    try {
      // Fetch ROM capacity
      const romResponse = await axios.get(`${env.VITE_CHOICE_API}/kapasitas_rom`, {
        headers: { Authorization: `Bearer ${localStorage.getItem('accessToken')}` },
      });
      this.items.ROMCapacity = romResponse.data;

      // Fetch RAM capacity
      const ramResponse = await axios.get(`${env.VITE_CHOICE_API}/kapasitas_ram`, {
        headers: { Authorization: `Bearer ${localStorage.getItem('accessToken')}` },
      });
      this.items.RAMCapacity = ramResponse.data;

      // Fetch RAM speed
      const ramSpeedResponse = await axios.get(`${env.VITE_CHOICE_API}/kecepatan_ram`, {
        headers: { Authorization: `Bearer ${localStorage.getItem('accessToken')}` },
      });
      this.items.RAMSpeed = ramSpeedResponse.data;

      // Fetch resolution
      const resolutionResponse = await axios.get(`${env.VITE_CHOICE_API}/resolusi`, {
        headers: { Authorization: `Bearer ${localStorage.getItem('accessToken')}` },
      });
      this.items.resolution = resolutionResponse.data;

      // Fetch processor
      const processorResponse = await axios.get(`${env.VITE_CHOICE_API}/generasi_processor`, {
        headers: { Authorization: `Bearer ${localStorage.getItem('accessToken')}` },
      });
      this.items.processor = processorResponse.data;

    } catch (err) {
      console.error('Error fetching data:', err);
    }
  },
  components: {
   InputSelect,
   InputText
  },
  methods: {
    validateForm (value) {
      if (typeof value === 'object' && value !== null) {
        for (const v of Object.values(value)) {
          if (this.validateForm(v)) return true;
        }
      }

      return (
        value === undefined ||
        value === null ||
        (typeof value === 'object' && Object.keys(value).length === 0) ||
        (typeof value === 'string' && value.trim().length === 0)
      );
    },
    handleSubmit() {
      console.log(this.formData)
      console.log(this.validateForm(this.formData))
      if(this.validateForm(this.formData)) return;
      console.log("Form submitted:", this.formData);
    },
    handleCancel() {
      this.formData = {
        laptopType: "",
        price: "",
        weight: "",
        processor: "",
        ROMCapacity: "",
        RAMCapacity: "",
        RAMSpeed: "",
      };
      console.log("Form reset");
      this.$emit('close');
    },
  },
};
</script>