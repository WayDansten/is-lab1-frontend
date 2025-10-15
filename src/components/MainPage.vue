<script setup>
import {
  DataTable,
  Column,
  Button,
  IftaLabel,
  InputText,
  InputNumber,
  Dialog,
  Select,
  Textarea,
  Stepper,
  StepList,
  StepPanels,
  StepPanel,
  Step,
  DatePicker,
  Message,
  Card,
  useToast,
  Toolbar,
  Popover,
} from 'primevue'
import { ref } from 'vue'

const labWorks = ref([{ id: 1 }, { id: 4 }, { id: 3 }, { id: 2 }, { id: 5 }])
const selectedLabWork = ref(null)

const filters = ref({ id: { value: null, matchMode: 'startsWith' } })

const isCreateDialogVisible = ref(false)

const deleteByIdValue = ref()
const deleteByAuthorValue = ref()
const countByAveragePointValue = ref()
const lowerDifficultyIdValue = ref()
const lowerDifficultyDifficultyValue = ref()

const labworkName = ref()
const labworkDescription = ref()
const labworkDifficulty = ref()
const labworkMinimalPoint = ref()
const labworkAveragePoint = ref()

const authorName = ref()
const authorEyeColor = ref()
const authorHairColor = ref()
const authorBirthday = ref()
const authorNationality = ref()

const locationName = ref()
const locationX = ref()
const locationY = ref()
const locationZ = ref()

const disciplineName = ref()
const disciplinePracticeHours = ref()

const coordinatesX = ref()
const coordinatesY = ref()

const difficulties = ['VERY_EASY', 'NORMAL', 'INSANE', 'IMPOSSIBLE']
const colors = ['BLACK', 'BLUE', 'YELLOW', 'BROWN']
const countries = ['UNITED_KINGDOM', 'USA', 'FRANCE', 'SOUTH_KOREA', 'NORTH_KOREA']

const isLabworkNameValid = ref(true)
const isLabworkMinimalPointValid = ref(true)
const isLabworkAveragePointValid = ref(true)
const isDisciplineNameValid = ref(true)
const isDisciplinePracticeHoursValid = ref(true)
const isCoordinatesXValid = ref(true)
const isCoordinatesYValid = ref(true)
const isAuthorNameValid = ref(true)
const isAuthorHairColorValid = ref(true)
const isAuthorBirthdayValid = ref(true)
const isAuthorNationalityValid = ref(true)
const isLocationNameValid = ref(true)
const isLocationXValid = ref(true)
const isLocationYValid = ref(true)
const isLocationZValid = ref(true)

// Toast messages
const toast = useToast()

function showToast(params) {
  toast.add(params)
}

function bakeToast(message, isSuccessful) {
  if (isSuccessful) {
    showToast({
      severity: 'success',
      summary: 'Success!',
      detail: message,
      life: 5000,
    })
  } else {
    showToast({
      severity: 'error',
      summary: 'Error!',
      detail: message,
      life: 5000,
    })
  }
}

// Client data update functions

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

// createForm functions (validation, request submission)

const validateStage1 = (activateCallback) => {
  isLabworkNameValid.value = true
  isLabworkMinimalPointValid.value = true
  isLabworkAveragePointValid.value = true

  if (labworkName.value === undefined) {
    isLabworkNameValid.value = false
  }

  if (labworkMinimalPoint.value !== undefined && labworkMinimalPoint.value <= 0) {
    isLabworkMinimalPointValid.value = false
  }

  if (labworkAveragePoint.value === undefined || labworkAveragePoint.value <= 0) {
    isLabworkAveragePointValid.value = false
  }

  if (
    isLabworkNameValid.value &&
    isLabworkMinimalPointValid.value &&
    isLabworkAveragePointValid.value
  ) {
    activateCallback('2')
  }
}

const validateStage2 = (activateCallback) => {
  isDisciplineNameValid.value = true
  isDisciplinePracticeHoursValid.value = true

  if (disciplineName.value === undefined) {
    isDisciplineNameValid.value = false
  }

  if (disciplinePracticeHours.value === undefined || disciplinePracticeHours.value < 1) {
    isDisciplinePracticeHoursValid.value = false
  }

  if (isDisciplineNameValid.value && isDisciplinePracticeHoursValid.value) {
    activateCallback('3')
  }
}

const validateStage3 = (activateCallback) => {
  isCoordinatesXValid.value = true
  isCoordinatesYValid.value = true

  if (coordinatesX.value === undefined) {
    isCoordinatesXValid.value = false
  }

  if (coordinatesY.value === undefined || coordinatesY.value < -566) {
    isCoordinatesYValid.value = false
  }

  if (isCoordinatesXValid.value && isCoordinatesYValid.value) {
    activateCallback('4')
  }
}

const validateStage4 = (activateCallback) => {
  isAuthorNameValid.value = true
  isAuthorHairColorValid.value = true
  isAuthorBirthdayValid.value = true
  isAuthorNationalityValid.value = true

  if (authorName.value === undefined) {
    isAuthorNameValid.value = false
  }

  if (authorHairColor.value === undefined) {
    isAuthorHairColorValid.value = false
  }

  if (authorBirthday.value === undefined) {
    isAuthorBirthdayValid.value = false
  }

  if (authorNationality.value === undefined) {
    isAuthorNationalityValid.value = false
  }

  if (
    isAuthorNameValid.value &&
    isAuthorHairColorValid.value &&
    isAuthorBirthdayValid.value &&
    isAuthorNationalityValid.value
  ) {
    activateCallback('5')
  }
}

const validateStage5 = () => {
  isLocationNameValid.value = true
  isLocationXValid.value = true
  isLocationYValid.value = true
  isLocationZValid.value = true

  if (locationName.value === undefined || locationName.value.length > 246) {
    isLocationNameValid.value = false
  }

  if (locationX.value === undefined) {
    isLocationXValid.value = false
  }

  if (locationY.value === undefined) {
    isLocationYValid.value = false
  }

  if (locationZ.value === undefined) {
    isLocationZValid.value = false
  }

  if (
    isLocationNameValid.value &&
    isLocationXValid.value &&
    isLocationYValid.value &&
    isLocationZValid.value
  ) {
    createEntry()
  }
}

async function createEntry() {
  const body = {
    name: labworkName.value,
    description: labworkDescription.value,
    difficulty: labworkDifficulty.value,
    minimalPoint: labworkMinimalPoint.value,
    averagePoint: labworkAveragePoint.value,
    discipline: {
      name: disciplineName.value,
      practiceHours: disciplinePracticeHours.value,
    },
    coordinates: {
      x: coordinatesX.value,
      y: coordinatesY.value,
    },
    author: {
      name: authorName.value,
      eyeColor: authorEyeColor.value,
      hairColor: authorHairColor.value,
      birthday: new Date(authorBirthday.value).toISOString().slice(0, -1),
      nationality: authorNationality.value,
      location: {
        name: locationName.value,
        x: locationX.value,
        y: locationY.value,
        z: locationZ.value,
      },
    },
  }

  const response = await fetch('http://localhost:8080/lab1/api/labwork', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(body),
  })
  const data = await response.json()

  bakeToast(data.message, response.ok)
}

// Functions for functionPanel inputs

async function deleteById() {
  if (deleteByIdValue.value === undefined) {
    bakeToast('"LabWork ID" field is empty', false)
  } else {
    const response = await fetch(
      `http://localhost:8080/lab1/api/labwork/${deleteByIdValue.value}`,
      {
        method: 'DELETE',
      },
    )
    const data = await response.json()

    bakeToast(data.message, response.ok)
  }
}

async function deleteByAuthor() {
  if (deleteByAuthorValue.value === undefined) {
    bakeToast('"Author name" field is empty', false)
  } else {
    const response = await fetch('http://localhost:8080/lab1/api/labwork/author', {
      method: 'DELETE',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ string: deleteByAuthorValue.value }),
    })
    const data = await response.json()

    bakeToast(data.message, response.ok)
  }
}

async function countByAveragePoint() {
  if (countByAveragePointValue.value === undefined) {
    bakeToast('"Average point value" field is empty', false)
  } else {
    const params = new URLSearchParams({ averagePoint: countByAveragePointValue.value })
    const response = await fetch(`http://localhost:8080/lab1/api/labwork/average_point?${params}`)
    const data = await response.json()

    bakeToast(data.message, response.ok)
  }
}

async function lowerDifficulty() {
  if (lowerDifficultyIdValue.value === undefined) {
    bakeToast('"LabWork ID" field is empty', false)
  } else if (lowerDifficultyDifficultyValue.value === undefined) {
    bakeToast('"Difficulty" field is empty', false)
  } else {
    const response = await fetch(
      `http://localhost:8080/lab1/api/labwork/${lowerDifficultyIdValue.value}`,
      {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ difficulty: lowerDifficultyDifficultyValue.value }),
      },
    )
    const data = response.json()

    bakeToast(data.message, response.ok)
  }
}
</script>

<template>
  <div id="bgPanel">
    <div id="blurPanel">
      <div id="toolbarPanel">
        <Toolbar>
          <template #center>
            <Button
              label="Create new entry"
              size="small"
              severity="info"
              @click="isCreateDialogVisible = true"
            ></Button>
            <Button
              label="Count by greater Average Point"
              size="small"
              severity="warn"
              @click="countByAveragePoint"
            ></Button>
            <Button
              label="Lower the Difficulty"
              size="small"
              severity="warn"
              @click="lowerDifficulty"
            ></Button>
            <Button label="Delete by ID" size="small" severity="warn" @click="deleteById"></Button>
            <Button
              label="Delete by Author"
              size="small"
              severity="warn"
              @click="deleteByAuthor"
            ></Button>
          </template>
        </Toolbar>
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
        <Stepper value="1" linear>
          <StepList>
            <Step value="1">Lab work parameters</Step>
            <Step value="2">Discipline</Step>
            <Step value="3">Coordinates</Step>
            <Step value="4">Author</Step>
            <Step value="5">Location</Step>
          </StepList>
          <StepPanels>
            <StepPanel v-slot="{ activateCallback }" value="1">
              <IftaLabel>
                <InputText
                  id="formNameInput"
                  v-model="labworkName"
                  variant="filled"
                  placeholder="Required"
                ></InputText>
                <Message v-if="!isLabworkNameValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formNameInput">Name</label>
              </IftaLabel>

              <IftaLabel>
                <Textarea
                  id="formDescriptionInput"
                  v-model="labworkDescription"
                  variant="filled"
                ></Textarea>
                <label for="formDescriptionInput">Description</label>
              </IftaLabel>

              <IftaLabel>
                <Select
                  id="formDifficultyInput"
                  v-model="labworkDifficulty"
                  :options="difficulties"
                  variant="filled"
                ></Select>
                <label for="formDifficultyInput">Difficulty</label>
              </IftaLabel>

              <IftaLabel>
                <InputNumber
                  id="formMinimalPointInput"
                  v-model="labworkMinimalPoint"
                  variant="filled"
                  :use-grouping="false"
                  :min-fraction-digits="0"
                  :max-fraction-digits="5"
                  placeholder="Greater than 0"
                ></InputNumber>
                <Message v-if="!isLabworkMinimalPointValid" severity="error"
                  >Field needs to be greater than 0</Message
                >
                <label for="formMinimalPointInput">Minimal point</label>
              </IftaLabel>

              <IftaLabel>
                <InputNumber
                  id="formAveragePointInput"
                  v-model="labworkAveragePoint"
                  variant="filled"
                  :use-grouping="false"
                  :min-fraction-digits="0"
                  :max-fraction-digits="5"
                  placeholder="Required; value > 0"
                ></InputNumber>
                <Message v-if="!isLabworkAveragePointValid" severity="error"
                  >Field is required to proceed and needs to be greater than 0</Message
                >
                <label for="formAveragePointInput">Average point</label>
              </IftaLabel>

              <div style="display: flex; justify-content: flex-end">
                <Button label="Next" @click="validateStage1(activateCallback)"></Button>
              </div>
            </StepPanel>

            <StepPanel v-slot="{ activateCallback }" value="2">
              <IftaLabel>
                <InputText
                  id="formDisciplineNameInput"
                  v-model="disciplineName"
                  variant="filled"
                  placeholder="Required"
                ></InputText>
                <Message v-if="!isDisciplineNameValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formDisciplineNameInput">Discipline name</label>
              </IftaLabel>

              <IftaLabel>
                <InputNumber
                  id="formDisciplinePracticeHoursInput"
                  v-model="disciplinePracticeHours"
                  variant="filled"
                  :use-grouping="false"
                  placeholder="Required; value >= 1"
                ></InputNumber>
                <Message v-if="!isDisciplinePracticeHoursValid" severity="error"
                  >Field is required to proceed and needs to be greater than or equal to 1</Message
                >
                <label for="formDisciplinePracticeHoursInput">Practice hours</label>
              </IftaLabel>

              <div style="display: flex; justify-content: space-between">
                <Button label="Back" @click="activateCallback('1')"></Button>
                <Button label="Next" @click="validateStage2(activateCallback)"></Button>
              </div>
            </StepPanel>

            <StepPanel v-slot="{ activateCallback }" value="3">
              <IftaLabel>
                <InputNumber
                  id="formCoordinatesXInput"
                  v-model="coordinatesX"
                  variant="filled"
                  :use-grouping="false"
                  :min-fraction-digits="0"
                  :max-fraction-digits="5"
                  placeholder="Required"
                ></InputNumber>
                <Message v-if="!isCoordinatesXValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formCoordinatesXInput">X coordinate</label>
              </IftaLabel>

              <IftaLabel>
                <InputNumber
                  id="formCoordinatesYInput"
                  v-model="coordinatesY"
                  variant="filled"
                  :use-grouping="false"
                  :min-fraction-digits="0"
                  :max-fraction-digits="5"
                  placeholder="Required; value >= -566"
                ></InputNumber>
                <Message v-if="!isCoordinatesYValid" severity="error"
                  >Field is required to proceed and needs to be greater than or equal to
                  -566</Message
                >
                <label for="formCoordinatesYInput">Y coordinate</label>
              </IftaLabel>

              <div style="display: flex; justify-content: space-between">
                <Button label="Back" @click="activateCallback('2')"></Button>
                <Button label="Next" @click="validateStage3(activateCallback)"></Button>
              </div>
            </StepPanel>

            <StepPanel v-slot="{ activateCallback }" value="4">
              <IftaLabel>
                <InputText
                  id="formAuthorNameInput"
                  v-model="authorName"
                  variant="filled"
                  placeholder="Required"
                ></InputText>
                <Message v-if="!isAuthorNameValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formAuthorNameInput">Author name</label>
              </IftaLabel>

              <IftaLabel>
                <Select
                  id="formAuthorEyeColorInput"
                  v-model="authorEyeColor"
                  :options="colors"
                  variant="filled"
                ></Select>
                <label for="formAuthorEyeColorInput">Eye color</label>
              </IftaLabel>

              <IftaLabel>
                <Select
                  id="formAuthorHairColorInput"
                  v-model="authorHairColor"
                  :options="colors"
                  variant="filled"
                  placeholder="Required"
                ></Select>
                <Message v-if="!isAuthorHairColorValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formAuthorHairColorInput">Hair color</label>
              </IftaLabel>

              <IftaLabel>
                <DatePicker
                  id="formAuthorBirthdayInput"
                  v-model="authorBirthday"
                  variant="filled"
                  placeholder="Required"
                ></DatePicker>
                <Message v-if="!isAuthorBirthdayValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formAuthorBirthdayInput">Birthday</label>
              </IftaLabel>

              <IftaLabel>
                <Select
                  id="formAuthorNationalityInput"
                  v-model="authorNationality"
                  :options="countries"
                  variant="filled"
                  placeholder="Required"
                ></Select>
                <Message v-if="!isAuthorNationalityValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formAuthorNationalityInput">Nationality</label>
              </IftaLabel>

              <div style="display: flex; justify-content: space-between">
                <Button label="Back" @click="activateCallback('3')"></Button>
                <Button label="Next" @click="validateStage4(activateCallback)"></Button>
              </div>
            </StepPanel>

            <StepPanel v-slot="{ activateCallback }" value="5">
              <IftaLabel>
                <InputText
                  id="formLocationNameInput"
                  v-model="locationName"
                  variant="filled"
                  placeholder="Required; 246 characters at most"
                ></InputText>
                <Message v-if="!isLocationNameValid" severity="error"
                  >Field is required to proceed and can be 246 characters long at most</Message
                >
                <label for="formLocationNameInput">Location name</label>
              </IftaLabel>

              <IftaLabel>
                <InputNumber
                  id="formLocationXInput"
                  v-model="locationX"
                  variant="filled"
                  :use-grouping="false"
                  placeholder="Required"
                ></InputNumber>
                <Message v-if="!isLocationXValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formLocationXInput">X coordinate</label>
              </IftaLabel>

              <IftaLabel>
                <InputNumber
                  id="formLocationYInput"
                  v-model="locationY"
                  variant="filled"
                  :use-grouping="false"
                  :min-fraction-digits="0"
                  :max-fraction-digits="5"
                  placeholder="Required"
                ></InputNumber>
                <Message v-if="!isLocationYValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formLocationYInput">Y coordinate</label>
              </IftaLabel>

              <IftaLabel>
                <InputNumber
                  id="formLocationZInput"
                  v-model="locationZ"
                  variant="filled"
                  :use-grouping="false"
                  :min-fraction-digits="0"
                  :max-fraction-digits="5"
                  placeholder="Required"
                ></InputNumber>
                <Message v-if="!isLocationZValid" severity="error"
                  >Field is required to proceed</Message
                >
                <label for="formLocationZInput">Z coordinate</label>
              </IftaLabel>

              <div style="display: flex; justify-content: space-between">
                <Button label="Back" @click="activateCallback('4')"></Button>
                <Button label="Create" severity="success" @click="validateStage5"></Button>
              </div>
            </StepPanel>
          </StepPanels>
        </Stepper>
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

#toolbarPanel .p-toolbar {
  background-color: rgba(0, 0, 0, 0.35);
  border: none;
  border-radius: 0;
}

#toolbarPanel .p-button {
  margin: 0.25rem;
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

#createForm .p-textarea,
#createForm .p-select,
#createForm .p-inputtext,
#createForm .p-inputnumber,
#createForm .p-datepicker,
#createForm .p-message {
  margin-bottom: 1.5rem;
}

:deep(.p-step-title) {
  font-family: 'Tektur', sans-serif !important;
}
</style>
