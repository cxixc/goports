<template>
    <div id="status" v-loading="logLoading" element-loading-background="transparent">
        <p class="status">总内存:{{ status.totleMem }} 已用:{{ status.usedMem }} 未用:{{ status.unusedMem }}</p>
        <p class="status">CPU全局使用率:{{ status.usedCPU }}</p>
        <p class="status">当前进程CPU使用率:{{ status.currentProcessUsedCPU }}</p>
        <p class="status">进程协程数:{{ status.goroutine }} 占用内存:{{ status.processUsedMem }}</p>
        <p class="status">goports全局连接数:{{ status.currentConnections }} </p>
        <p class="status">goports全局限制连接数:{{ status.maxConnections }}</p>



    </div>
</template>


<style>
#status {
    height: 95vh;
}

.status {
    font-size: 15px;
}

#proxys_status {
    font-size: 10px;
    list-style: none;
    padding: 0;
    margin: 0;

    text-align: left;
    margin-left: 0px;
}
</style>

<script setup lang="ts">

import { apiGetStatus } from '../apis/utils'
import { onMounted, onUnmounted, ref,inject } from 'vue'
import { ElNotification } from 'element-plus'

const global:any = inject("global")



var logLoading = ref(true)

const clickTest = ()=>{
ElNotification({
    title: 'Prompt',
    message: 'This is a message that does not automatically close',
    duration: 0,
  })
}


var status=ref({totleMem: '0m',
            usedMem:'0m',
            unusedMem:'0m',
            usedCPU:"0%",
            currentProcessUsedCPU:"0%",
            goroutine:"0",
            processUsedMem:"0m",
            currentConnections:0,
            maxConnections:0,
            proxysStatus:""})

var timerID:any

function flushStatus() {
    if(global.currentPage.value!="#status"){
        return ;
    }

    apiGetStatus().then((res) => {
         logLoading.value =false
        status.value = res.data
    })
}



onMounted(() => {
    flushStatus();
    timerID = setInterval(() => {
        flushStatus();
    }, 3000);
});

onUnmounted(() => {
    //console.log("onUnmounted status page ")
    clearInterval(timerID)
});



</script>


