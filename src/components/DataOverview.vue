<script setup>
import { ref } from 'vue'

const header = ["URL","의사 이름","환자 나이","환자 성별","가족력","기저질환", "암시적 증상", "명시적 증상", "진단", "외부 자료"]
var table = [
                    ['java',1,2,1,1,16],
                    ['c',2,1,2,2,1],
                    ['c++',3,4,3,3,2],
                    ['python',4,7,6,11,23],
                    ['JS',17,18,8,7,20],
                    ['Vue',10,15,28,45,100]
                ]

function updateTable(tableData) {
    table = tableData
    tableKey.value++
}

const tableKey = ref(0)

defineExpose({
    updateTable
})

</script>

<template>
    <div id = "tab" :key="tableKey">
        <h3> 데이터 목록 </h3>
        <table>
            <thead>
                <th v-for = "item in header">{{item}}</th>
            </thead>
            <tbody>
                <tr v-for="line in table">
                    <td v-for="item in line">{{ item }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

h3 {
    font-size: 30px;
    text-align: center;
    border-bottom: solid rgb(214, 222, 230);
}

table {
    width: 75%;
    text-align : left;
    margin: auto;
    text-align: center;
}

table th {
    padding : 12px;
    border-bottom: 2px solid  darkgray;
}

table td {
    padding : 12px;
}

table tr:nth-of-type(even) {
    background-color: rgba(0,0,255,0.1);
 }

 #tab {
    background-color:aliceblue;
    border: solid rgb(214, 222, 230);
    border-radius: 10px;
 }
</style>
