<script setup>
import { Button, DataTable, Column, InputText, Textarea, InputNumber, IftaLabel, Select, Dialog, Card } from 'primevue'
import { ref } from 'vue'
import CreateForm from '@/components/CreateForm.vue'
import FunctionToolbar from '@/components/FunctionToolbar.vue'
import { useToastNotifier } from '@/composables/useToast'
import 'primeicons/primeicons.css'

// Toasts

const { bakeToast } = useToastNotifier()

// DataTable binds

const difficulties = ['VERY_EASY', 'NORMAL', 'INSANE', 'IMPOSSIBLE']

const labWorks = ref([{id: 1, description: 'Basic description'}])
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

// Editing panels toggles

const isEditingDescription = ref(false)
const isEditingDetails = ref(false)
const isEditingAuthor = ref(false)

const toggleEditingDescription = () => {
  isEditingDescription.value = !isEditingDescription.value
}

const toggleEditingDetails = () => {
  isEditingDetails.value = !isEditingDetails.value
}

const toggleEditingAuthor = () => {
  isEditingAuthor.value = !isEditingAuthor.value
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
        <Card id="descriptionPanel" class="data-card">
          <template #title>
            <div class="card-title">
              <span>Lab work description</span>
              <Button v-if="selectedLabWork && !isEditingDescription" icon="pi pi-pencil" size="small" class="edit-button" @click="toggleEditingDescription"></Button>
              <Button v-if="selectedLabWork && isEditingDescription" icon="pi pi-times" size="small" class="edit-button" @click="toggleEditingDescription"></Button>
              <Button v-if="selectedLabWork && isEditingDescription" icon="pi pi-check" size="small" class="edit-button" @click="toggleEditingDescription"></Button>
            </div>
          </template>
          <template #content>
            <template v-if="isEditingDescription">
              <Textarea variant="filled" v-model="selectedLabWork.description"></Textarea>
            </template>
            <template v-else-if="selectedLabWork">
              <div class="card-details">{{ selectedLabWork.description }}</div>
            </template>
            <template v-else>Select a lab work to read its description</template>
          </template>
        </Card>
        <Card id="detailPanel" class="data-card">
          <template #title>
            <div class="card-title">
              <span>Lab work details</span>
              <Button v-if="selectedLabWork && !isEditingDetails" icon="pi pi-pencil" size="small" class="edit-button" @click="toggleEditingDetails"></Button>
              <Button v-if="selectedLabWork && isEditingDetails" icon="pi pi-times" size="small" class="edit-button" @click="toggleEditingDetails"></Button>
              <Button v-if="selectedLabWork && isEditingDetails" icon="pi pi-check" size="small" class="edit-button" @click="toggleEditingDetails"></Button>
            </div>
          </template>
          <template #content>
            <template v-if="isEditingDetails">
              <IftaLabel>
                <InputNumber
                  id="coordinatesYInput"
                  v-model="selectedLabWork.coordinates.y"
                  variant="filled"
                  :use-grouping="false"
                  :min-fraction-digits="0"
                  :max-fraction-digits="5"
                ></InputNumber>
                <label for="coordinatesYInput">Y</label>
              </IftaLabel>
            </template>
            <template v-else-if="selectedLabWork">
              <div class="card-details">
                <strong>Coordinates: </strong>
                <span>X: {{ selectedLabWork.coordinates?.x }}</span>
                <span>Y: {{ selectedLabWork.coordinates?.y }}</span>
              </div>
              <div class="card-details">
                <strong>Discipline: </strong>
                <span>Name: {{ selectedLabWork.discipline?.name }}</span>
                <span>Practice hours: {{ selectedLabWork.discipline?.practiceHours }}</span>
              </div>
            </template>
            <template v-else> Select a lab work to read about its details </template>
          </template>
        </Card>
        <Card id="authorPanel" class="data-card">
          <template #title>
            <div class="card-title">
              <span>About the author</span>
              <Button v-if="selectedLabWork && !isEditingAuthor" icon="pi pi-pencil" size="small" class="edit-button" @click="toggleEditingAuthor"></Button>
              <Button v-if="selectedLabWork && isEditingAuthor" icon="pi pi-times" size="small" class="edit-button" @click="toggleEditingAuthor"></Button>
              <Button v-if="selectedLabWork && isEditingAuthor" icon="pi pi-check" size="small" class="edit-button" @click="toggleEditingAuthor"></Button>
            </div></template>
          <template #content>
            <template v-if="isEditingAuthor">

            </template>
            <template v-else-if="selectedLabWork">
              <div class="card-details">
                <span>Name: {{ selectedLabWork.author?.name }}</span>
                <span>Eye color: {{ selectedLabWork.author?.eyeColor }}</span>
                <span>Hair color: {{ selectedLabWork.author?.hairColor }}</span>
                <span>Nationality: {{ selectedLabWork.author?.nationality }}</span>
                <span>Birthday: {{ selectedLabWork.author?.birthday }}</span>
              </div>
              <div class="card-details">
                <strong>Location</strong>
                <span>Name: {{ selectedLabWork.author?.location.name }}</span>
                <span>X: {{ selectedLabWork.author?.location.x }}</span>
                <span>Y: {{ selectedLabWork.author?.location.y }}</span>
                <span>Z: {{ selectedLabWork.author?.location.z }}</span>
              </div>
            </template>
            <template v-else> Select a lab work to read about its author </template>
          </template>
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

.card-title > * {
  padding: 0.25rem;
}

.card-details {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  padding: 0.25rem;

  font-family: 'Roboto', sans-serif;
}

.edit-button {
  margin-left: auto;
  background: transparent !important;
  border: none !important;
  color: rgba(255, 255, 255, 0.6) !important;
  width: 2rem;
  height: 2rem;
}

.edit-button:hover {
  background: rgba(255, 255, 255, 0.1) !important;
  color: rgba(255, 255, 255, 0.9) !important;
}

.edit-button:active {
  background: rgba(255, 255, 255, 0.2) !important;
  color: rgba(255, 255, 255, 1) !important;
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
