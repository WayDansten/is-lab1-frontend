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

const formName = ref()
const formDescription = ref()
const formDifficulty = ref()
const formMinimalPoint = ref()
const formAveragePoint = ref()

const formAuthorName = ref()
const formAuthorEyeColor = ref()
const formAuthorHairColor = ref()
const formAuthorBirthday = ref()
const formAuthorNationality = ref()

const formLocationName = ref()
const formLocationX = ref()
const formLocationY = ref()
const formLocationZ = ref()

const formDisciplineName = ref()
const formDisciplinePracticeHours = ref()

const formCoordinatesX = ref()
const formCoordinatesY = ref()

const difficulties = ['Very easy', 'Normal', 'Insane', 'Impossible']
const colors = ['Black', 'Blue', 'Yellow', 'Brown']
const countries = ['United Kingdom', 'USA', 'France', 'South Korea', 'North Korea']

function switchPanels(targetPanel) {
  activePanel.value = ''
  setTimeout(() => {
    activePanel.value = targetPanel
  }, 600)
}

function logOut() {
  router.push('/auth')
}

async function createEntry() {
  console.log('Entry created')
}
</script>

<template>
  <div id="bgPanel">
    <div id="blurPanel">
      <div id="tablePanel">
        <DataTable id="dataTable" :value="labWorks" paginator :rows="5">
          <Column field="id" header="Id"></Column>
          <Column field="name" header="Name"></Column>
          <Column field="discipline" header="Discipline"></Column>
          <Column field="author_id" header="Author ID"></Column>
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
                <Button label="Delete by ID" size="large" severity="warn"></Button>
                <IftaLabel>
                  <InputNumber
                    id="deleteByIdInput"
                    v-model="deleteByIdValue"
                    variant="filled"
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
                <InputText id="formNameInput" v-model="formName" variant="filled"></InputText>
                <label for="formNameInput">Name</label>
              </IftaLabel>
              <IftaLabel>
                <Textarea
                  id="formDescriptionInput"
                  v-model="formDescription"
                  variant="filled"
                ></Textarea>
                <label for="formDescriptionInput">Description</label>
              </IftaLabel>
              <IftaLabel>
                <Select
                  id="formDifficultyInput"
                  v-model="formDifficulty"
                  :options="difficulties"
                  variant="filled"
                ></Select>
                <label for="formDifficultyInput">Difficulty</label>
              </IftaLabel>
              <IftaLabel>
                <InputNumber
                  id="formMinimalPointInput"
                  v-model="formMinimalPoint"
                  variant="filled"
                ></InputNumber>
                <label for="formMinimalPointInput">Minimal point</label>
              </IftaLabel>
              <IftaLabel>
                <InputNumber
                  id="formAveragePointInput"
                  v-model="formAveragePoint"
                  variant="filled"
                ></InputNumber>
                <label for="formAveragePointInput">Average point</label>
              </IftaLabel>
              <div style="display: flex; justify-content: flex-end">
                <Button label="Next" @click="activateCallback('2')"></Button>
              </div>
            </StepPanel>

            <StepPanel v-slot="{ activateCallback }" value="2">
              <IftaLabel>
                <InputText
                  id="formDisciplineNameInput"
                  v-model="formDisciplineName"
                  variant="filled"
                ></InputText>
                <label for="formDisciplineNameInput">Discipline name</label>
              </IftaLabel>
              <IftaLabel>
                <InputNumber
                  id="formDisciplinePracticeHoursInput"
                  v-model="formDisciplinePracticeHours"
                  variant="filled"
                ></InputNumber>
                <label for="formDisciplinePracticeHoursInput">Practice hours</label>
              </IftaLabel>
              <div style="display: flex; justify-content: space-between">
                <Button label="Back" @click="activateCallback('1')"></Button>
                <Button label="Next" @click="activateCallback('3')"></Button>
              </div>
            </StepPanel>

            <StepPanel v-slot="{ activateCallback }" value="3">
              <IftaLabel>
                <InputNumber
                  id="formCoordinatesXInput"
                  v-model="formCoordinatesX"
                  variant="filled"
                ></InputNumber>
                <label for="formCoordinatesXInput">X coordinate</label>
              </IftaLabel>
              <IftaLabel>
                <InputNumber
                  id="formCoordinatesYInput"
                  v-model="formCoordinatesY"
                  variant="filled"
                ></InputNumber>
                <label for="formCoordinatesYInput">Y coordinate</label>
              </IftaLabel>
              <div style="display: flex; justify-content: space-between">
                <Button label="Back" @click="activateCallback('2')"></Button>
                <Button label="Next" @click="activateCallback('4')"></Button>
              </div>
            </StepPanel>

            <StepPanel v-slot="{ activateCallback }" value="4">
              <IftaLabel>
                <InputText
                  id="formAuthorNameInput"
                  v-model="formAuthorName"
                  variant="filled"
                ></InputText>
                <label for="formAuthorNameInput">Author name</label>
              </IftaLabel>
              <IftaLabel>
                <Select
                  id="formAuthorEyeColorInput"
                  v-model="formAuthorEyeColor"
                  :options="colors"
                  variant="filled"
                ></Select>
                <label for="formAuthorEyeColorInput">Eye color</label>
              </IftaLabel>
              <IftaLabel>
                <Select
                  id="formAuthorHairColorInput"
                  v-model="formAuthorHairColor"
                  :options="colors"
                  variant="filled"
                ></Select>
                <label for="formAuthorHairColorInput">Hair color</label>
              </IftaLabel>
              <IftaLabel>
                <DatePicker
                  id="formAuthorBirthdayInput"
                  v-model="formAuthorBirthday"
                  variant="filled"
                ></DatePicker>
                <label for="formAuthorBirthdayInput">Birthday</label>
              </IftaLabel>
              <IftaLabel>
                <Select
                  id="formAuthorNationalityInput"
                  v-model="formAuthorNationality"
                  :options="countries"
                  variant="filled"
                ></Select>
                <label for="formAuthorNationalityInput">Nationality</label>
              </IftaLabel>
              <div style="display: flex; justify-content: space-between">
                <Button label="Back" @click="activateCallback('3')"></Button>
                <Button label="Next" @click="activateCallback('5')"></Button>
              </div>
            </StepPanel>

            <StepPanel v-slot="{ activateCallback }" value="5">
              <IftaLabel>
                <InputText
                  id="formLocationNameInput"
                  v-model="formLocationName"
                  variant="filled"
                ></InputText>
                <label for="formLocationNameInput">Location name</label>
              </IftaLabel>
              <IftaLabel>
                <InputNumber
                  id="formLocationXInput"
                  v-model="formLocationX"
                  variant="filled"
                ></InputNumber>
                <label for="formLocationXInput">X coordinate</label>
              </IftaLabel>
              <IftaLabel>
                <InputNumber
                  id="formLocationYInput"
                  v-model="formLocationY"
                  variant="filled"
                ></InputNumber>
                <label for="formLocationYInput">Y coordinate</label>
              </IftaLabel>
              <IftaLabel>
                <InputNumber
                  id="formLocationZInput"
                  v-model="formLocationZ"
                  variant="filled"
                ></InputNumber>
                <label for="formLocationZInput">Z coordinate</label>
              </IftaLabel>
              <div style="display: flex; justify-content: space-between">
                <Button label="Back" @click="activateCallback('4')"></Button>
                <Button label="Create" severity="success" @click="createEntry"></Button>
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
#createForm .p-inputnumber {
  width: 100%;
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
