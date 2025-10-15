<script setup>
import { DataTable, Column, InputText, Dialog, Card } from 'primevue'
import { ref } from 'vue'
import CreateForm from '@/components/CreateForm.vue'
import FunctionToolbar from '@/components/FunctionToolbar.vue'

const labWorks = ref([{ id: 1 }, { id: 4 }, { id: 3 }, { id: 2 }, { id: 5 }])
const selectedLabWork = ref(null)

const filters = ref({ id: { value: null, matchMode: 'startsWith' } })

const isCreateDialogVisible = ref(false)

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

function toggleCreateForm() {
  isCreateDialogVisible.value = !isCreateDialogVisible.value
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
          removable-sort
          :value="labWorks"
          paginator
          :rows="5"
          v-model:filters="filters"
          filter-display="menu"
          selection-mode="single"
          v-model:selection="selectedLabWork"
        >
          <template #empty>No entries found. Create one!</template>
          <Column field="id" header="Id" sortable>
            <template #filter="{ filterModel, filterCallback }">
              <InputText
                v-model="filterModel.value"
                @input="filterCallback"
                placeholder="Search by ID"
              ></InputText>
            </template>
          </Column>
          <Column field="name" header="Name" sortable></Column>
          <Column field="difficulty" header="Difficulty" sortable></Column>
          <Column field="creationDate" header="Creation date" sortable></Column>
          <Column field="minimalPoint" header="Minimal point" sortable></Column>
          <Column field="averagePoint" header="Average point" sortable></Column>
          <Column field="coordinates" header="Coordinates" sortable></Column>
        </DataTable>
      </div>
      <div id="bottomPanel">
        <Card id="descriptionPanel" class="data-card">
          <template #title>Lab work description</template>
          <template #content>{{
            selectedLabWork === null
              ? 'Select a lab work to read its description'
              : selectedLabWork.description
          }}</template>
        </Card>
        <Card id="detailPanel" class="data-card">
          <template #title>Lab work details</template>
          <template #content>
            {{
              selectedLabWork === null
                ? 'Select a lab work to view its details'
                : selectedLabWork.description
            }}</template
          >
        </Card>
        <Card id="authorPanel" class="data-card">
          <template #title>About the author</template>
          <template #content>{{
            selectedLabWork === null
              ? 'Select a lab work to read about its author'
              : selectedLabWork.author
          }}</template>
        </Card>
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

.data-card {
  display: flex;
  justify-content: center;
  height: 100%;

  background: rgba(0, 0, 0, 0.35);
  border-radius: 0%;
  box-shadow: none;
}

/* Specific element styles */

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
