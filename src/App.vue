<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
// import { ref, reactive, onMounted } from 'vue'
import { read, utils, writeFileXLSX } from 'xlsx/xlsx.mjs';
import dataFile from './data.xlsx?b64'

// console.log({ dataFile })
const records = ref([])
const resultText = ref("")
function exportFile() {
  const data = records.value
  const res = {}
  for (const row of data) {
    let e = res[row.手机号]
    if (e) {
      if (e['总分'] < row['总分']) {
        e['总分'] = row['总分']
      }
    } else {
      res[row.手机号] = row
    }
  }
  const uniqueRows = Object.values(res)
  console.log({ uniqueRows })
  const ws = utils.json_to_sheet(uniqueRows);
  const wb = utils.book_new();
  utils.book_append_sheet(wb, ws, "Data");
  writeFileXLSX(wb, "20大答题数据（去重）.xlsx");
  resultText.value = `读取到${data.length}人次，去重后有${uniqueRows.length}人参与考试`
}
async function readXlsx(event) {
  const f = event.target.files[0]
  const wb = read(await f.arrayBuffer());
  const data = utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]]);
  records.value = data.map(e => {
    let [姓名, 手机号, 身份证, 地址 ] = e.来源详情.split('/')
    let res = {
      姓名, 手机号, 身份证, 地址, IP: e['来自IP'], 分数: e['总分'], 时间: e.提交答卷时间}
    return res
  })
  // console.log(records.value)
  exportFile()
}
onMounted(() => {

})


</script>

<template>
    <div class="bk">
      <input type="file" @input="readXlsx" /> <span>{{resultText }}</span>
    </div>
</template>

<style scoped>
.bk {
  height: 600px;
  width: 90%;
  margin: auto;
}
</style>
