<script setup>
import router from '@/router/router'
import { DataTable, Column, Button, InputGroup } from 'primevue'
import { ref } from 'vue'
import TextField from './basic_components/TextField.vue'

const labWorks = ref([])

const activePanel = ref('info')

function switchPanels(targetPanel) {
  activePanel.value = ''
  setTimeout(() => {
    activePanel.value = targetPanel
  }, 600)
}

function logOut() {
  router.push('/auth')
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
                <TextField description="Lab work ID"></TextField>
              </InputGroup>
              <InputGroup>
                <Button label="Find by Description" size="large" severity="warn"></Button>
                <TextField description="Description prefix"></TextField>
              </InputGroup>
              <InputGroup>
                <Button label="Delete by ID" size="large" severity="warn"></Button>
                <TextField description="Lab work ID"></TextField>
              </InputGroup>
              <InputGroup>
                <Button label="Delete by Author" size="large" severity="warn"></Button>
                <TextField description="Author ID"></TextField>
              </InputGroup>
            </div>
            <div id="subFunctionsPanelRight">
              <Button label="Create new entry" size="large" severity="info"></Button>
              <InputGroup>
                <Button label="Modify by ID" size="large" severity="info"></Button>
                <TextField description="Lab work ID"></TextField>
              </InputGroup>
              <InputGroup>
                <Button
                  label="Count by greater Average Point"
                  size="large"
                  severity="info"
                ></Button>
                <TextField description="Average point value"></TextField>
              </InputGroup>
              <InputGroup>
                <Button label="Lower the Difficulty" size="large" severity="info"></Button>
                <TextField description="Lab work ID"></TextField>
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
