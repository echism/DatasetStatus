<script setup>
import jsonData from '../data/status.json';
import FilterButton from './FilterButton.vue';
import { ref, computed } from 'vue';
const filterOptions=[{"label":'All',"by":['complete','processing','queued','failed']},{"label":'Complete',"by":['complete']},{"label":'Active',"by":['queued','processing']},{"label":'Failed',"by":['failed']}]
const selectedFilter = ref(filterOptions[0]) 
const filteredItems = computed(() => {
  return jsonData.filter(item =>
    selectedFilter.value.by.includes(item.status)
  )
})
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
    <div>
        <FilterButton v-for="filter in filterOptions" :key="filter.label" :label="filter.label" @click="selectedFilter=filter" />
        <table>
        <thead>
            <tr>
                <th scope="col">Dataset</th>
                <th scope="col">Status</th>
                <th scope="col">Size</th>
                <th scope="col">Last Updated</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="item in filteredItems" :key="item.id" >
                <td>{{ item.name }}</td>
                <td>{{ item.status }}</td>
                <td>{{convertByte(item.size_bytes)}}</td>
                <td>{{ dateConvert(item.updated_at)}}</td>
            </tr>
        </tbody>
    </table>

    </div>
</template>