<template>
  <div class="container">
    <a-row :gutter="30" justify="center" class="input-section">
      <a-col :span="12" class="textarea-section">
        <span>Git</span>
        <a-textarea v-model:value="gitLog" placeholder="Paste logs from smartgit" />
      </a-col>
      <a-col :span="12" class="textarea-section">
        <span>Filezilla</span>
        <a-textarea v-model:value="filezillaLog" placeholder="Paste logs from Filezilla" />
      </a-col>
    </a-row>
    <a-row>
      <a-col :span="24">
        <a-button style="height: 40px; width: 120px" type="primary" @click="compare">
          Compare
        </a-button>
      </a-col>
    </a-row>
    <guide />
  </div>

  <a-modal
    v-model:open="openResultModal"
    width="calc(100vw - 50px)"
    title="Compare result"
    :footer="null"
    centered
    style="max-width: 1200px"
  >
    <a-space size="large">
      <p style="color: #22c55e">File Success: {{ compareResult.success }}</p>
      <p style="color: #ef4444">File Error:{{ compareResult.error }}</p>
    </a-space>
    <a-table
      class="ant-table-striped"
      :columns="columns"
      :data-source="compareResultList"
      bordered
      :pagination="false"
      :scroll="{ y: 'calc(100vh - 200px)' }"
      :row-class-name="rowClassName"
    >
      <template #bodyCell="{ column, text, index }">
        <template v-if="column.dataIndex === 'no'">
          <a-flex align="start" justify="center">
            {{ index + 1 }}
          </a-flex>
        </template>
        <template v-else-if="column.dataIndex === 'filePath'">
          {{ text }}
        </template>
        <template v-else-if="column.dataIndex === 'match'">
          <a-flex align="start" justify="center">
            <check-circle-two-tone two-tone-color="#52c41a" v-if="text" />
            <close-circle-two-tone two-tone-color="#eb2f96" v-else />
          </a-flex>
        </template>
      </template>
    </a-table>
  </a-modal>
</template>

<script setup>
import { ref } from 'vue'
import Guide from './Components/Guide.vue'

import { CloseCircleTwoTone, CheckCircleTwoTone } from '@ant-design/icons-vue'

const gitLog = ref()
const filezillaLog = ref()
const compareResultList = ref([])
const compareResult = ref({})
const openResultModal = ref(false)

const columns = [
  {
    title: 'No',
    dataIndex: 'no',
    width: 60
  },
  {
    title: 'File',
    dataIndex: 'filePath'
  },
  {
    title: 'Result',
    dataIndex: 'match',
    width: 100
  }
]

function rowClassName(_record, index) {
  let className = index % 2 === 1 ? 'table-striped' : null
  if (!_record.match) {
    className += ' row-error'
  }
  return className
}

function compare() {
  if (!gitLog.value || !filezillaLog.value) {
    alert('Log missing')
    return
  }

  compareResultList.value = []
  let lines = gitLog.value.trim().split(/\r?\n|\r|\n/g)
  let countSuccess = 0
  let countError = 0
  lines.forEach(function (line) {
    let [file_name, modification, directory] = line.split('\t')
    let filePath = `${directory}/${file_name}`

    const regex = new RegExp('Status:\\s+Starting upload of\\s+.*?' + filePath)
    const match = filezillaLog.value.match(regex)

    compareResultList.value.push({
      filePath,
      match: !!match
    })
    if (match) {
      countSuccess++
    } else {
      countError++
    }

    compareResult.value['success'] = countSuccess
    compareResult.value['error'] = countError

    openResultModal.value = true
  })
}
</script>

<style scoped>
.container {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  flex-direction: column;
  position: relative;
}
.input-section {
  width: 100%;
  height: calc(100vh - 150px);
  margin-bottom: 20px;
}
.textarea-section {
  display: flex;
  flex-direction: column;
}
.textarea-section span {
  display: inline-block;
  margin-bottom: 10px;
  font-size: 16px;
  font-weight: bold;
}
.textarea-section textarea {
  flex-grow: 1;
}

.ant-table-striped :deep(.table-striped) td {
  background-color: #fafafa;
}
:deep(.row-error) td {
  color: #ef4444;
}
</style>
