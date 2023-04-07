<script setup>
import "vue3-multiselect-checkboxed/style.css"
import Multiselect from '@vueform/multiselect'
import { ref, watch } from 'vue'

const link = ref()
const docName = ref()
const newDoc = ref()

const myKey = ref(0)
const linkKey = ref(0)

var linkList = []
var doctorList = []

const isLinkDup = ref(false)
var isLinkEmpty = true

function addDoctor(newDocName) {
    if (newDocName == undefined) {
        alert("추가할 의사 이름을 입력 해주세요.")
        return
    }
    else {
        if (doctorList.indexOf(newDocName) != -1) {
            alert("이미 목록에 있는 의사입니다.")
            return
        }
        alert("의사(" + newDocName + ") 추가 완료.")
        doctorList.push(newDocName)
        newDoc.value = undefined
        myKey.value++
    }
}

function updateLinkList(newLink) {
    linkList = newLink
}

function updateDocList(docList) {
    doctorList = docList
    myKey.value++
}

function checkDup() {
    if (linkList.indexOf(link.value) == -1) {
        isLinkDup.value = false
    }
    else { isLinkDup.value = true }
    console.log(isLinkDup)
}

function checkEmpty() {
    if (link.value == "") {
        isLinkEmpty = true;
    }
    else {isLinkEmpty = false;}

    checkDup()
    linkKey.value++
}

function flush() {
    link.value = undefined
    docName.value = undefined
}

defineExpose({
  link,
  docName,
  isLinkDup,
  isLinkEmpty,
  updateDocList,
  updateLinkList,
  flush
})

</script>
<template>
    <div class="top">
        <div class="line">
            <div class="inputText">
                <p :key="linkKey">링크: {{ isLinkEmpty ? "" : (isLinkDup ? "링크 중복" : "확인") }}</p>
                <input type="text" class="textInput" v-model="link" placeholder="링크" @input="checkEmpty"/>
            </div>
        </div>
        <div class="lineS">
            <div class="selectDoc">
                <div class="list">
                    <div class="right">
                        <div class="formHead">
                            <p>의사: </p>
                        </div>
                        <Multiselect
                        v-model="docName"
                        :searchable="true"
                        :options="doctorList"
                        :key="myKey"
                        ></Multiselect>
                    </div>
                </div>
                <div class="add">
                    <button @click="addDoctor(newDoc)">
                    추가하기
                    </button>
                    <input type="text" class="textInputS" v-model="newDoc" placeholder="새 의사" />
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.top {
    padding-top: 80px;
}

.right {
    margin: auto;
    padding: auto;
    width: 50%;
    height: 100%;
    float: right;
}

.inputText {
    width: 100%;
    margin: auto;
    text-align: center;
}

.selectDoc {
    width:100%;
    margin: auto;
    display:flex;
    text-align: center;
    padding: auto;
    margin-top: 50px;
}

.list {
    width: 50%;
    padding: auto;
    float: right;
}

.line, .lineS {
    padding: 30px;
    border-top: solid #b2b5bb;
    background-color: #ececec;
}

.lineS {
    border-bottom: solid #b2b5bb;
}

.add {
    width:50%;
    text-align: left;
    padding: 10px;
}
.textInput, .textInputS {
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

</style>
<style src="@vueform/multiselect/themes/default.css"></style>
