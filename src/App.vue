<script setup>
import "vue3-multiselect-checkboxed/style.css"
import DataOverview from "./components/DataOverview.vue"
import InitialInput from "./components/InitialInput.vue"
import InputFormOne from "./components/InputFormOne.vue"
import InputFormTwo from "./components/InputFormTwo.vue"
import TextField from "./components/TextField.vue"
import { ref } from 'vue'
import xlsx from 'xlsx/dist/xlsx.full.min.js'


const XLSX = xlsx

const pageNum = ref(0)
const lastPageNum = 3

const dataOverview = ref(null)
const initialInput = ref(null)
const inputFormOne = ref(null)
const inputFormTwo = ref(null)
const textField = ref(null)

const mainKey = ref(0)
const disKey = ref(0)

var workbook

const sheetName = "LOL"

const linkCol = 'A'
const docNameCol = 'B'
const sympCol = ['G', 'H']
const diseaseCol = ['E', 'F', 'I']

var isXslxLoaded = false
var isTextLoaded = false

function nextPage() {
  pageNum.value++
}

function prevPage() {
  pageNum.value--
}

function validCheck() {
  console.log(initialInput.value.isLinkDup)
  if (initialInput.value.link == undefined || initialInput.value.isLinkDup) return [false, "링크"]
  if (initialInput.value.docName == undefined) return [false, "의사 이름"]
  if (inputFormOne.value.sex == undefined) return [false, "성별"]
  return [true, null]
}

function injectAll() {
  var valid = []

  valid = validCheck()
  if (!valid[0]) {
    alert(valid[1] + " 필드에 유효하지 않은 값이 있습니다.")
    return
  }

  var newRow = [[
    initialInput.value.link, 
    initialInput.value.docName, 
    inputFormOne.value.age,
    inputFormOne.value.sex,
    inputFormTwo.value.fHist.join(),
    inputFormTwo.value.bgnd.join(),
    inputFormOne.value.impSymps.join(),
    inputFormOne.value.expSymps.join(),
    inputFormOne.value.diseases.join(),
    inputFormTwo.value.externalInfo
  ]]
  console.log(workbook.Sheets[sheetName]["!ref"])
  var ws = XLSX.utils.sheet_add_aoa(workbook.Sheets[sheetName], newRow, { origin: -1 })
  console.log(ws)
  workbook = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(workbook, ws, sheetName)

  createTable(workbook)
  initSelector(workbook)
  initialInput.value.flush()
  inputFormOne.value.flush()
  inputFormTwo.value.flush()
  pageNum.value = 0
}

function exportAll() {
  XLSX.writeFile(workbook, "out.xlsx")
}

function importXSLX(e) {
  var file = e.target.files[0]
  const reader = new FileReader()
  reader.onload = (evt) => {
    if (evt.target.readyState === FileReader.DONE) {
      workbook = XLSX.read(evt.target.result, {tpye: "array"})
      initSelector(workbook)
      createTable(workbook)
    }
  }
  reader.readAsArrayBuffer(file)
  isXslxLoaded = true
  mainKey.value++
}

function initSelector(workbook) {
  var sheet = workbook.Sheets[sheetName]
  var range = XLSX.utils.decode_range(sheet['!ref'])['e']['r']

  var linkList = []
  var docList = []
  var sympList = []
  var disList = []

  for (var i = 1; i < range + 1; i++) {
    var linkCell = linkCol + (i+1)
    var link = sheet[linkCell]['v']

    if (linkList.indexOf(link) == -1) {
      linkList.push(link)
    }
  }
  initialInput.value.updateLinkList(linkList)

  for (var i = 1; i < range + 1; i++) {
    var docCell = docNameCol + (i+1)
    var docName = sheet[docCell]['v']

    if (docList.indexOf(docName) == -1) {
      docList.push(docName)
    }
  }
  initialInput.value.updateDocList(docList)

  for (var i = 1; i < range + 1; i++) {
    for (var col of sympCol) {
      var sympCell = col + (i+1)
      var sympString = ""
      var sympString = sheet[sympCell] == undefined ? "" : sheet[sympCell]['v']
      var sympArr = sympString.split(',')

      for (var symp of sympArr) {
        if (sympList.indexOf(symp) == -1) {
          sympList.push(symp)
        }
      }
    }
  }
  console.log(sympList)
  inputFormOne.value.updateSympList(sympList)

  for (var i = 1; i < range + 1; i++) {
    for (var col of diseaseCol) {
      var disCell = col + (i+1)
      var disString = sheet[disCell]['v']
      var disArr = disString.split(',')
      
      for (var dis of disArr) {
        if (disList.indexOf(dis) == -1) {
          disList.push(dis)
        }
      }
    }
  }
  console.log(disList)
  inputFormOne.value.updateDiseaseList(disList, disKey)
  inputFormTwo.value.updateDiseaseList(disList, disKey)
}

function createTable(book) {
  var sheet = book.Sheets['LOL']
  var rowRange = XLSX.utils.decode_range(sheet['!ref'])['e']['r']
  var colRange = XLSX.utils.decode_range(sheet['!ref'])['e']['c']
  
  var tableData = []

  for (var i = 1; i < rowRange + 1; i++) {
    var rowData = []
    for (var j = 0; j < colRange + 1; j++) {
      var target = XLSX.utils.encode_col(j) + (i+1)
      rowData.push(sheet[target] == undefined ? "" : sheet[target]['v'])
    }
    tableData.push(rowData)
  }

  dataOverview.value.updateTable(tableData)
}

function importText(e) {
  var file = e.target.files[0]
  const reader = new FileReader()
  reader.onload = (evt) => {
    if (evt.target.readyState === FileReader.DONE) {
      console.log(evt.target.result)
      textField.value.getText(evt.target.result)
      textField.value.textKey++
    }
  }
  reader.readAsText(file)
  isTextLoaded = true
  mainKey.value++
}

</script>

<template>
  <header>
    <div class="fileSelector">
      엑셀 데이터 불러오기: 
      <input type="file" accept=".csv, .xlsx, appliction/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel" v-on:change="importXSLX" v-if="pageNum == 0"/>
    </div>
    <div class="fileSelector">
      크롤링 데이터 불러오기: 
      <input type="file" accept=".txt" v-on:change="importText" v-if="pageNum == 0"/>
    </div>
  </header>
  
  <div class="inputField" :key="mainKey" v-show="isXslxLoaded">
    <DataOverview ref="dataOverview" v-show="pageNum == 0"></DataOverview>
    <InitialInput ref="initialInput" v-show="pageNum == 1"></InitialInput>
    <InputFormOne ref="inputFormOne" v-show="pageNum == 2"></InputFormOne>
    <InputFormTwo ref="inputFormTwo" v-show="pageNum == 3"></InputFormTwo>
  </div>
  <div class="textField" :key="mainKey" v-show="isTextLoaded">
    <TextField ref="textField" v-show="pageNum == 0"></TextField>
  </div>
  <div class="footer" v-show="isXslxLoaded">
    <button id="export" @click="exportAll()" v-if="pageNum == 0">
      내보내기
    </button>
    <button id="prev" @click="prevPage()" v-if="pageNum > 0">
      이전으로
    </button> 
    <button id="next" @click="nextPage()" v-if="pageNum < lastPageNum">
      다음으로
    </button>
    <button id="confirm" @click="injectAll()" v-if="pageNum == lastPageNum">
      확인
    </button>
  </div>
</template>

<style scoped>
header {
  padding: 20px;
  line-height: 1;
  height: 200px;
  width: 100%;
  text-align: center;
  display: flex;
}

.fileSelector {
  margin: auto;
}

.inputField, .textField {
  border-top: solid;
  border-bottom: solid;
  border-radius: 10px;
  height: 60%;
  overflow-y: auto;
  margin-bottom: 10px;
}

.footer {
  padding-top: calc(100vh - (40% + 200px));
  width: 100%;
  height: 110px;
  bottom: 0px;
}

#next, #confirm {
  float:right;
}
</style>
