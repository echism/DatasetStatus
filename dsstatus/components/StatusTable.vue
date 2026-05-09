<script setup>
import jsonData from '../data/status.json';
import FilterButton from './FilterButton.vue';
import { ref, computed } from 'vue';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faCheckCircle,faClock,faUsers,faTriangleExclamation } from '@fortawesome/free-solid-svg-icons';
const statIndicators={"complete":[faCheckCircle,"darkgreen"],"processing":[faClock,"darkblue"],"queued":[faUsers,"darkblue"],"failed":[faTriangleExclamation,"firebrick"]}
const filterOptions=[{"label":'All',"by":['complete','processing','queued','failed']},{"label":'Complete',"by":['complete']},{"label":'Active',"by":['queued','processing']},{"label":'Failed',"by":['failed']}]
const selectedFilter = ref(filterOptions[0]) 
const filteredItems = computed(() => {
  return jsonData.filter(item =>
    selectedFilter.value.by.includes(item.status)
  )
})
const searchQuery = ref('');
const curatedItems = computed(() => {
  const query = searchQuery.value.toLowerCase().trim();

  return filteredItems.value.filter(item => {
    
    if (!query) return true;

    const dateString = dateConvert(item.updated_at).toLowerCase();
    return (
      item.name.toLowerCase().includes(query) ||
      item.status.toLowerCase().includes(query)||
      convertByte(item.size_bytes).toLowerCase().includes(query) ||
      dateString.includes(query)
    );
  });
});

const byteDes =['B','KB','MB','GB','TB','PB','EB']
function convertByte(bytes){
    let byteIndex = 0;
    while (byteIndex<byteDes.length && bytes>1024){
        byteIndex++;
        bytes/=1024;
    }
return Math.floor(bytes)+byteDes[byteIndex];
    
}
function dateConvert(dt){
    let unconverted = new Date(dt);
    return unconverted.toDateString()+' '+unconverted.toLocaleTimeString();
}

</script>
<template>
    <div class="m-4">
        <HeaderTitle />
        <div class="bg-mauve-600 w-full py-1 px-3">
            <fieldset>
                <legend class="sr-only">Filter datasets</legend>
                <filters-list class="flex flex-col md:flex-row px-2 py-2 md:gap-2">
                    <FilterButton v-for="item in filterOptions" :key="item.label" :label="item.label" @click="selectedFilter=item" />
                </filters-list>
            </fieldset>
        <label for="search" class="sr-only">Search datasets</label>
        <input class="shadow-md shadow-sky-950 rounded-sm mx-4 bg-olive-100 " type="text" v-model="searchQuery" placeholder="Search Datasets" />
        </div>
        <table class="table-auto w-full text-left whitespace-no-wrap bg-taupe-100 rounded-md shadow-xl shadow-mauve-200 border-2">
            <caption class="sr-only">Dataset listing with status, size, and last updated time</caption>
            <thead class="font-light" > 
            <tr class=" bg-mauve-900 text-olive-50">
                <th scope="col" class="text-center">dataset</th>
                <th scope="col" class="text-center" >status</th>
                <th scope="col" class="text-center" >size</th>
                <th scope="col" class="text-center" >last updated</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="item in curatedItems" :key="item.id" class="hover:bg-slate-300 ">
                <td>{{ item.name }}</td>
                <td class="statusCell"><span class="pr-4"><font-awesome-icon :icon="statIndicators[item.status][0]" class="p-2" :style="{ color: statIndicators[item.status][1] }"/></span>{{ item.status}}</td>
                <td>{{convertByte(item.size_bytes)}}</td>
                <td>{{ dateConvert(item.updated_at)}}</td>
            </tr>
        </tbody>
    </table>

    </div>
</template>