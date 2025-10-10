<script setup>
import router from '@/router/router'
import {
  DataTable,
  Column,
  Button,
  InputGroup,
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
  useToast,
} from 'primevue'
import { ref } from 'vue'

const labWorks = ref([])

const activePanel = ref('info')

const isCreateDialogVisible = ref(false)

const findByIdValue = ref()
const findByDescriptionValue = ref()
const deleteByIdValue = ref()
const deleteByAuthorValue = ref()
const modifyByIdValue = ref()
const countByAveragePointValue = ref()
const lowerTheDifficultyByIdValue = ref()

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

function bakeResponseToast(response, message) {
  if (response.ok) {
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

// Functions for sending requests
async function fetchControlRequest(url, method, body = null) {
  const params = {
    method: method,
    headers: {
      'Content-type': 'application/json',
    },
  }

  if (body && ['POST', 'PUT', 'PATCH', 'DELETE'].includes(method)) {
    params.body = JSON.stringify(body)
  }

  const response = await fetch(url, params)
  const data = await response.json()

  bakeResponseToast(response, data.message)
}

async function fetchDataRequest(url) {
  const response = await fetch(url)
  const data = await response.json()

  return data
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
  const data = await fetchDataRequest('http://localhost:8080/lab1/api/labwork')
  labWorks.value = data
}

// Panel/page switching functions

function switchPanels(targetPanel) {
  activePanel.value = ''
  setTimeout(() => {
    activePanel.value = targetPanel
  }, 600)
}

function logOut() {
  router.push('/auth')
}

// createForm functions (validation, request submission)

const validateStage1 = (activateCallback) => {
  isLabworkNameValid.value = true
  isLabworkMinimalPointValid.value = true
  isLabworkAveragePointValid.value = true

  if (labworkName.value === null || labworkName.value === '') {
    isLabworkNameValid.value = false
  }

  if (labworkMinimalPoint.value !== null && labworkMinimalPoint.value <= 0) {
    isLabworkMinimalPointValid.value = false
  }

  if (labworkAveragePoint.value === null || labworkAveragePoint.value <= 0) {
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

  if (disciplineName.value === null || disciplineName.value === '') {
    isDisciplineNameValid.value = false
  }

  if (disciplinePracticeHours.value === null || disciplinePracticeHours.value < 1) {
    isDisciplinePracticeHoursValid.value = false
  }

  if (isDisciplineNameValid.value && isDisciplinePracticeHoursValid.value) {
    activateCallback('3')
  }
}

const validateStage3 = (activateCallback) => {
  isCoordinatesXValid.value = true
  isCoordinatesYValid.value = true

  if (coordinatesX.value === null) {
    isCoordinatesXValid.value = false
  }

  if (coordinatesY.value === null || coordinatesY.value < -566) {
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

  if (authorName.value === null || authorName.value === '') {
    isAuthorNameValid.value = false
  }

  if (authorHairColor.value === null) {
    isAuthorHairColorValid.value = false
  }

  if (authorBirthday.value === null) {
    isAuthorBirthdayValid.value = false
  }

  if (authorNationality.value === null) {
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

  if (locationName.value === null || locationName.value.length > 246) {
    isLocationNameValid.value = false
  }

  if (locationX.value === null) {
    isLocationXValid.value = false
  }

  if (locationY.value === null) {
    isLocationYValid.value = false
  }

  if (locationZ.value === null) {
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

  await fetchControlRequest('http://localhost:8080/lab1/api/labwork', 'POST', body)
}

// Functions for functionPanel inputs

async function deleteById() {
  if (deleteByIdValue.value === null || deleteByIdValue.value === '') {
    //
  }

  await fetchControlRequest(
    `http://localhost:8080/lab1/api/labwork/${deleteByIdValue.value}`,
    'DELETE',
  )
}
</script>

<template>
  <div id="bgPanel">
    <div id="blurPanel">
      <div id="tablePanel">
        <DataTable id="dataTable" :value="labWorks" paginator :rows="5">
          <Column field="id" header="Id"></Column>
          <Column field="name" header="Name"></Column>
          <Column field="difficulty" header="Difficulty"></Column>
          <Column field="creationDate" header="Creation date"></Column>
          <Column field="minimalPoint" header="Minimal point"></Column>
          <Column field="averagePoint" header="Average point"></Column>
          <Column field="coordinates" header="Coordinates"></Column>
        </DataTable>
      </div>
      <div id="bottomPanel">
        <Transition name="fade">
          <div id="infoPanel" v-if="activePanel === 'info'">
            <div id="descriptionPanel">
              <h3>Lab work description</h3>
            </div>
            <div id="authorPanel">
              <h3>About the author</h3>
            </div>
          </div>
        </Transition>
        <Transition name="fade">
          <div id="functionsPanel" v-if="activePanel === 'functions'">
            <div id="subFunctionsPanelLeft">
              <InputGroup>
                <Button label="Find by ID" size="large" severity="warn"></Button>
                <IftaLabel>
                  <InputNumber
                    id="findByIdInput"
                    v-model="findByIdValue"
                    variant="filled"
                    :use-grouping="false"
                  ></InputNumber>
                  <label for="findByIdInput">Lab work ID</label>
                </IftaLabel>
              </InputGroup>
              <InputGroup>
                <Button label="Find by Description" size="large" severity="warn"></Button>
                <IftaLabel>
                  <InputText
                    id="findByDescriptionInput"
                    v-model="findByDescriptionValue"
                    variant="filled"
                  ></InputText>
                  <label for="findByDescriptionInput">Description prefix</label>
                </IftaLabel>
              </InputGroup>
              <InputGroup>
                <Button
                  label="Delete by ID"
                  size="large"
                  severity="warn"
                  @click="deleteById"
                ></Button>
                <IftaLabel>
                  <InputNumber
                    id="deleteByIdInput"
                    v-model="deleteByIdValue"
                    variant="filled"
                    :use-grouping="false"
                  ></InputNumber>
                  <label for="deleteByIdInput">Lab work ID</label>
                </IftaLabel>
              </InputGroup>
              <InputGroup>
                <Button label="Delete by Author" size="large" severity="warn"></Button>
                <IftaLabel>
                  <InputNumber
                    id="deleteByAuthorInput"
                    v-model="deleteByAuthorValue"
                    variant="filled"
                    :use-grouping="false"
                  ></InputNumber>
                  <label for="deleteByAuthorInput">Author ID</label>
                </IftaLabel>
              </InputGroup>
            </div>
            <div id="subFunctionsPanelRight">
              <Button
                label="Create new entry"
                size="large"
                severity="info"
                @click="isCreateDialogVisible = true"
              ></Button>
              <InputGroup>
                <Button label="Modify by ID" size="large" severity="info"></Button>
                <IftaLabel>
                  <InputNumber
                    id="modifyByIdInput"
                    v-model="modifyByIdValue"
                    variant="filled"
                    :use-grouping="false"
                  ></InputNumber>
                  <label for="modifyByIdInput">Lab work ID</label>
                </IftaLabel>
              </InputGroup>
              <InputGroup>
                <Button
                  label="Count by greater Average Point"
                  size="large"
                  severity="info"
                ></Button>
                <IftaLabel>
                  <InputNumber
                    id="countByAveragePointInput"
                    v-model="countByAveragePointValue"
                    variant="filled"
                    :use-grouping="false"
                    :min-fraction-digits="0"
                    :max-fraction-digits="5"
                  ></InputNumber>
                  <label for="countByAveragePointInput">Average point value</label>
                </IftaLabel>
              </InputGroup>
              <InputGroup>
                <Button label="Lower the Difficulty" size="large" severity="info"></Button>
                <IftaLabel>
                  <InputNumber
                    id="lowerTheDifficultyByIdInput"
                    v-model="lowerTheDifficultyByIdValue"
                    variant="filled"
                    :use-grouping="false"
                  ></InputNumber>
                  <label for="lowerTheDifficultyByIdInput">Lab work ID</label>
                </IftaLabel>
              </InputGroup>
            </div>
          </div>
        </Transition>
        <div id="controlPanel">
          <div id="subControlPanelTop">
            <Transition name="fade">
              <Button
                id="showFunctionsPanelButton"
                label="Show functions"
                size="large"
                severity="info"
                v-if="activePanel === 'info'"
                @click="switchPanels('functions')"
              ></Button>
            </Transition>
            <Transition name="fade">
              <Button
                id="showInfoPanelButton"
                label="Show info"
                size="large"
                severity="info"
                v-if="activePanel === 'functions'"
                @click="switchPanels('info')"
              ></Button>
            </Transition>
          </div>
          <div id="subControlPanelBottom">
            <Button
              id="logOutButton"
              label="Log out"
              size="large"
              severity="warn"
              @click="logOut"
            ></Button>
          </div>
        </div>
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

  background: rgba(0, 0, 0, 0.377);
  backdrop-filter: blur(10px);
}

#tablePanel {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 50vh;
}

#bottomPanel {
  position: fixed;
  top: 50%;
  left: 0;
  width: 100vw;
  height: 50vh;
}

#infoPanel {
  position: fixed;
  top: 50%;
  left: 0;
  width: 70vw;
  height: 50vh;
}

#functionsPanel {
  position: fixed;
  top: 50%;
  left: 0;
  width: 70vw;
  height: 50vh;
}

#subFunctionsPanelLeft {
  position: fixed;
  top: 50%;
  left: 0;
  width: 35vw;
  height: 50vh;

  display: flex;
  justify-content: space-evenly;
  align-items: center;
  flex-direction: column;
}

#subFunctionsPanelRight {
  position: fixed;
  top: 50%;
  left: 35%;
  width: 35vw;
  height: 50vh;

  display: flex;
  justify-content: space-evenly;
  align-items: center;
  flex-direction: column;
}

#descriptionPanel {
  position: fixed;
  top: 50%;
  left: 0;
  width: 35vw;
  height: 50vh;

  display: flex;
  justify-content: center;
}

#authorPanel {
  position: fixed;
  top: 50%;
  left: 35%;
  width: 35vw;
  height: 50vh;

  display: flex;
  justify-content: center;
}

#controlPanel {
  position: fixed;
  top: 50%;
  left: 70%;
  width: 30vw;
  height: 50vh;
}

#subControlPanelTop {
  position: fixed;
  top: 50%;
  left: 70%;
  width: 30vw;
  height: 25vh;

  display: flex;
  justify-content: center;
  align-items: center;
}

#subControlPanelBottom {
  position: fixed;
  top: 75%;
  left: 70%;
  width: 30vw;
  height: 25vh;

  display: flex;
  justify-content: center;
  align-items: center;
}

/* Specific element styles */

#subFunctionsPanelLeft .p-inputgroup,
#subFunctionsPanelRight .p-inputgroup {
  width: 70%;
}

#subFunctionsPanelRight .p-button {
  width: 70%;
}

#subFunctionsPanelLeft .p-inputgroup .p-button,
#subFunctionsPanelRight .p-inputgroup .p-button {
  width: 40%;
  flex-shrink: 0;
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
  background-color: rgba(0, 0, 0, 0.377);
}

:deep(.p-inputtext) {
  background-color: rgba(0, 0, 0, 0.377) !important;
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

/* Animations */

.fade-enter-active,
.fade-leave-active {
  transition: all 0.6s ease;
}

.fade-enter-from {
  opacity: 0;
}

.fade-leave-to {
  opacity: 0;
}

.fade-enter-to,
.fade-leave-from {
  opacity: 1;
}
</style>
