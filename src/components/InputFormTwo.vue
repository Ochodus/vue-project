<script setup>
import Multiselect from '@vueform/multiselect'
import { ref } from 'vue'

var diseaseList = []

const fHist = ref([])
const bgnd = ref([])

const newDises = ref()
var myDisKey = null

const externalInfo = ref("")

function addDis(newDis) {
  if (newDis == undefined) {
    alert("추가할 질병을 입력 해주세요.")
    return
  }
  else {
    diseaseList.push(newDis)
    alert("질병(" + newDis + ") 추가 완료.")
    newDises.value = undefined
    myDisKey.value++
  }
}

function updateDiseaseList(disList, disKey) {
  diseaseList = disList
  myDisKey = disKey
  myDisKey.value++
}

function flush(){
    fHist.value = []
    bgnd.value = []
    externalInfo.value = ""
}

defineExpose({
  fHist,
  bgnd,
  externalInfo,
  updateDiseaseList,
  flush
})

</script>

<template>
  <div>
    <div class="sympLine">
      <div class="inputDis">
        <div class="list">
          <div class="right">
            <div>가족력: {{ fHist.length > 0 ? fHist : '' }}</div>
            <Multiselect
              v-model="fHist"
              mode="tags"
              :colse-on-select="false"
              :searchable="true"
              :create-option="true"
              :options="diseaseList"
              :key="myDisKey"
            ></Multiselect>
          </div>
        </div>
      </div>
      <div class="inputDis">
        <div class="list">
          <div class="right">
            <div>기저질환: {{ bgnd.length > 0 ? bgnd : '' }}</div>
            <Multiselect
              v-model="bgnd"
              mode="tags"
              :colse-on-select="false"
              :searchable="true"
              :create-option="true"
              :options="diseaseList"
              :key="myDisKey"
            ></Multiselect>
          </div>
        </div>
      </div>
      <div class="addSymp">
        <button @click="addDis(newDises)">
        추가하기
        </button>
        <input type="text" class="textInputS" v-model="newDises" placeholder="새 질병" />
      </div>
    </div>
    <div class="lineS">
        <div class="inputText">
            <p>외부 자료: {{  }}</p>
            <input type="text" class="textInput" v-model="externalInfo" placeholder="외부 자료"/>
        </div>
    </div>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.inputDis {
  width:100%;
  display:flex;
  text-align: center;
}

.list {
  width: 50%;
  padding: auto;
  float: right;
  padding-top: 10px;
}

.addSymp {
  width:50%;
  text-align: left;
  padding: 10px;
}

.right {
    margin: auto;
    padding: auto;
    width: 100%;
    height: 100%;
    float: right;
}

.sympLine {
  display: flex;
}

.list {
  width: 90%;
}

.textInput, .textInputS, .textInputN {
    background: var(--ms-bg,#fff);
    border-radius: var(--ms-radius,4px);
    font-family: inherit;
    font-size: inherit;
    outline: none;
    padding-left: var(--ms-px,.875rem);
    background: var(--ms-bg,#fff);
    border: var(--ms-border-width,1px) solid var(--ms-border-color,#d1d5db);
    border-radius: var(--ms-radius,4px);
    box-sizing: border-box;
    display: flex;
    font-size: var(--ms-font-size,1rem);
    min-height: calc(var(--ms-border-width, 1px)*2 + var(--ms-font-size, 1rem)*var(--ms-line-height, 1.375) + var(--ms-py, .5rem)*2);
    position: relative;
    width: 50%;
}

.textInput {
    margin: 0 auto;
}

.lineS, .sympLine {
    padding: 30px;
    border-top: solid #b2b5bb;
    background-color: #ececec;
}

.lineS {
    border-bottom: solid #b2b5bb;
    text-align: center;
}


</style>
<style src="@vueform/multiselect/themes/default.css"></style>