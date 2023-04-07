<script setup>
import Multiselect from '@vueform/multiselect'
import { ref } from 'vue'

var symptomsList = []

var diseaseList = []

const age = ref("")
const sex = ref()

const impSymps = ref([])
const expSymps = ref([])
const diseases = ref([])

const newSymps = ref()
const newDises = ref()

const mySympKey = ref(0)
var myDisKey = null

function addSymps(newSymp) {
  if (newSymp == undefined) {
    alert("추가할 증상을 입력 해주세요.")
    return
  }
  else {
    if (symptomsList.indexOf(newSymp) != -1) {
      alert("이미 목록에 존재하는 증상입니다.")
      return
    }
    symptomsList.push(newSymp)
    alert("증상(" + newSymp + ") 추가 완료.")
    newSymps.value = undefined
    mySympKey.value++
  }
}

function addDis(newDis) {
  if (newDis == undefined) {
    alert("추가할 질병을 입력 해주세요.")
    return
  }
  else {
    if (diseaseList.indexOf(newDis) != -1) {
      alert("이미 목록에 존재하는 질병입니다.")
      return
    }
    diseaseList.push(newDis)
    alert("질병(" + newDis + ") 추가 완료.")
    newDises.value = undefined
    myDisKey.value++
  }
}

function updateSympList(sympList) {
  symptomsList = sympList
  mySympKey.value++
}

function updateDiseaseList(disList, disKey) {
  diseaseList = disList
  myDisKey = disKey
  myDisKey.value++
}

function flush() {
  age.value = ""
  sex.value = undefined
  impSymps.value = []
  expSymps.value = []
  diseases.value = []
}

defineExpose({
  age,
  sex,
  impSymps,
  expSymps,
  diseases,
  myDisKey,
  updateSympList,
  updateDiseaseList,
  flush
})

</script>

<template>
  <div>
    <div class="inputAge">
      <p>나이: </p>
      <input type="number" class="textInputN" v-model.number="age" placeholder="나이" />
    </div>
    <div class="inputSex">
      <p>성별: </p>
      <div class="selector">
        <input type="radio" id="male" value="남" v-model="sex" />
        <label for="male">남</label>
        <input type="radio" id="female" value="여" v-model="sex" />
        <label for="female">여</label>
        <input type="radio" id="other" value="기타" v-model="sex" />
        <label for="other">기타</label>
        <input type="radio" id="unknown" value="모름" v-model="sex" />
        <label for="unknown">모름</label>
      </div>
    </div>
    <div class="sympLine">
      <div class="inputSymps">
        <div class="list">
          <div class="right">
            <div>암시적 증상들: {{ impSymps.length > 0 ? impSymps : '' }}</div>
            <Multiselect
              v-model="impSymps"
              mode="tags"
              :colse-on-select="false"
              :searchable="true"
              :create-option="true"
              :options="symptomsList"
              :key="mySympKey"
            ></Multiselect>
          </div>
        </div>
      </div>
      <div class="inputSymps">
        <div class="list">
          <div class="right">
            <div>명시적 증상들: {{ expSymps.length > 0 ? expSymps : '' }}</div>
            <Multiselect
              v-model="expSymps"
              mode="tags"
              :colse-on-select="false"
              :searchable="true"
              :create-option="true"
              :options="symptomsList"
              :key="mySympKey"
            ></Multiselect>
          </div>
        </div>
      </div>
      <div class="addSymp">
        <button @click="addSymps(newSymps)">
        추가하기
        </button>
        <input type="text" class="textInputS" v-model="newSymps" placeholder="새 증상" />
      </div>
    </div>
    <div class="disLine">
      <div class="inputSymps">
        <div class="list">
          <div class="right">
            <div>진단명: {{ diseases.length > 0 ? diseases : '' }}</div>
            
            <Multiselect
              v-model="diseases"
              mode="tags"
              :colse-on-select="false"
              :searchable="true"
              :create-option="true"
              :options="diseaseList"
              :key="myDisKey"
            ></Multiselect>
          </div>
        </div>
        <div class="addSymp">
          <button @click="addDis(newDises)">
          추가하기
          </button>
          <input type="text" class="textInputS" v-model="newDises" placeholder="새 질병" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.inputAge {
  padding: 30px;
  text-align: center;
  border-top: solid #b2b5bb;
  background-color:#ececec;
}

.inputSex {
  text-align: center;
  padding: 30px;
  border-top: solid #b2b5bb;
  background-color:#ececec;
}

#male, #female, #other, #unknown {
  margin-left: 5px;
  margin-right: 5px;
}

.selector {
  margin-top: 5px;
}

.inputSymps {
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
  padding: 30px;
  border-top: solid #b2b5bb;
  background-color:#ececec;
}

.disLine {
  padding: 30px;
  border-top: solid #b2b5bb;
  border-bottom: solid #b2b5bb;
  background-color:#ececec;
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

.textInputN {
    width: 70px;
    margin: 0 auto;
}

</style>
<style src="@vueform/multiselect/themes/default.css"></style>