<template>
  <div class="flex flex-col">
    <div class="my-6">
      <TabView 
        v-for="tab in tabs" 
        :key="tab.id" 
        v-show="tab.id === activeTab"
        >
        <template v-slot:default>
          <component :is="tab.content" />
        </template>
      </TabView>
    </div>
    <TabMenu 
      :tabs="tabs" 
      :active-tab="activeTab" 
      @select-tab="selectTab" 
    />
  </div>
</template>

<script>
import TabMenu from './TabMenu.vue';
import TabView from './TabView.vue';
import TabKriteria from './Tabs/TabKriteria.vue';
import TabLaptop from './Tabs/TabLaptop.vue';

export default {
  components: {
    TabMenu,
    TabView,
    TabKriteria,
    TabLaptop,
  },
  data() {
    return {
      tabs: [
        { id: 'tab1', title: 'Data Kriteria', content: 'TabKriteria' },
        { id: 'tab2', title: 'Data Laptop', content: 'TabLaptop' },
        { id: 'tab3', title: 'Penilaian Laptop', content: 'TabPenilaian' },
        { id: 'tab4', title: 'Hasil Perhitungan', content: 'TabHasil' }
      ],
      activeTab: 'tab1'
    };
  },
  methods: {
    selectTab(tabId) {
      this.activeTab = tabId;
    }
  }
};
</script>
