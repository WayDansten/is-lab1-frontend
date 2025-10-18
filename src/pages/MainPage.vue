<script setup>
import { DataTable, Column, InputText, InputNumber, Select, Dialog } from 'primevue'
import { ref } from 'vue'
import 'primeicons/primeicons.css'
import { useToastNotifier } from '@/composables/useToast'
import CreateForm from '@/components/CreateForm.vue'
import FunctionToolbar from '@/components/FunctionToolbar.vue'
import DetailCards from '@/components/DetailCards.vue'

// Toasts

const { bakeToast } = useToastNotifier()

// DataTable binds

const difficulties = ['VERY_EASY', 'NORMAL', 'INSANE', 'IMPOSSIBLE']

const labWorks = ref([{ id: 1, description: 'Basic description', coordinates: { x: 10, y: 20 } }])
const selectedLabWork = ref(null)

const filters = ref({ id: { value: null, matchMode: 'startsWith' } })

const editingRows = ref([])

const onRowEditSave = async (event) => {
  let { newData, index } = event
  labWorks.value[index] = newData

  const response = await fetch('http://localhost:8080/lab1/api/labwork', {
    method: 'PATCH',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(newData),
  })
  const data = await response.json()

  bakeToast(data.string, response.ok)
}

const columns = ref([
  { field: 'id', header: 'ID', editable: false },
  { field: 'name', header: 'Name', editable: true },
  { field: 'difficulty', header: 'Difficulty', editable: true },
  { field: 'creationDate', header: 'Creation date', editable: false },
  {
    field: 'minimalPoint',
    header: 'Minimal point',
    editable: true,
  },
  {
    field: 'averagePoint',
    header: 'Average point',
    editable: true,
  },
])

// CreateForm toggle

const isCreateDialogVisible = ref(false)

function toggleCreateForm() {
  isCreateDialogVisible.value = !isCreateDialogVisible.value
}

// WebSocket functions

const socket = new WebSocket('ws://localhost:8080/lab1/ws')

socket.onopen = () => {
  refreshData()
}

socket.onmessage = () => {
  setTimeout(() => refreshData(), 200)
}

async function refreshData() {
  const response = await fetch('http://localhost:8080/lab1/api/labwork')
  const data = await response.json()
  labWorks.value = data
}
</script>

<template>
  <div id="bgPanel">
    <div id="blurPanel">
      <div id="toolbarPanel">
        <FunctionToolbar @create-entry="toggleCreateForm" />
      </div>
      <div id="tablePanel">
        <DataTable
          id="dataTable"
          :value="labWorks"
          paginator
          :rows="5"
          removable-sort
          v-model:filters="filters"
          filter-display="menu"
          selection-mode="single"
          v-model:selection="selectedLabWork"
          edit-mode="row"
          v-model:editing-rows="editingRows"
          @row-edit-save="onRowEditSave"
        >
          <template #empty>No entries found. Create one!</template>
          <Column selection-mode="single"></Column>
          <Column v-for="col in columns" :key="col.field" :field="col.field" :header="col.header">
            <template #editor="{ data, field }" v-if="col.editable">
              <template v-if="field === 'name'">
                <InputText v-model="data[field]" variant="filled"></InputText>
              </template>
              <template v-else-if="field === 'difficulty'">
                <Select v-model="data[field]" :options="difficulties" variant="filled"></Select>
              </template>
              <template v-else>
                <InputNumber
                  v-model="data[field]"
                  :use-grouping="false"
                  :min-fraction-digits="0"
                  :max-fraction-digits="5"
                  variant="filled"
                ></InputNumber>
              </template>
            </template>
          </Column>
          <Column :row-editor="true"></Column>
        </DataTable>
      </div>
      <div id="bottomPanel">
        <DetailCards v-model="selectedLabWork" />
      </div>
      <Dialog
        id="createForm"
        v-model:visible="isCreateDialogVisible"
        modal
        header="Create a new lab work entry"
      >
        <CreateForm />
      </Dialog>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Play:wght@400;700&family=Roboto:ital,wght@0,100..900;1,100..900&family=Tektur:wght@400..900&display=swap');

/* Panel styles and arrangement */

#bgPanel {
  background-image: url('src/assets/BG2.jpg');
  background-size: cover;
  background-repeat: no-repeat;

  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
}

#blurPanel {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;

  display: grid;
  grid-template-rows: auto 1fr 1fr;
  grid-template-columns: 1fr;
  gap: 1rem;

  background: rgba(0, 0, 0, 0.35);
  backdrop-filter: blur(10px);
}

#toolbarPanel {
  grid-row: 1;
}

#tablePanel {
  grid-row: 2;
}

#bottomPanel {
  grid-row: 3;

  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}

#tablePanel :deep(.p-datatable .p-datatable-paginator-bottom) {
  border: none;
}

:deep(#dataTable .p-datatable-thead > tr > th > *) {
  justify-content: center;
}

:deep(#dataTable .p-datatable-tbody > tr > td) {
  border: none;
  text-align: center;
  font-family: 'Roboto', sans-serif;
}

:deep(#dataTable .p-datatable-thead > tr > th),
:deep(#dataTable .p-datatable-tbody > tr),
:deep(.p-paginator) {
  background-color: rgba(0, 0, 0, 0.35);
}
</style>
