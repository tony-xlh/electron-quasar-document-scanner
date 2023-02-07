<template>
  <q-page class="index">
    <div class="row container">
      <div class="col-8" id="left">
        <div class="toolbar">
          <q-btn class="toolbar-btn" v-on:click="deleteSelected" icon="delete" />
          <q-btn class="toolbar-btn" v-on:click="edit" icon="edit" />
          <q-btn class="toolbar-btn" v-on:click="rotate" icon="rotate_right" />
          <q-btn class="toolbar-btn" v-on:click="moveUp" icon="arrow_upward" />
          <q-btn class="toolbar-btn" v-on:click="moveDown" icon="arrow_downward" />
        </div>
        <div class="dwt">
          <DWT 
            width="100%" 
            height="100%" 
            cols="2"
            rows="2"
            @onWebTWAINReady="onWebTWAINReady">
          </DWT>
        </div>
      </div>
      <div class="col-4" id="right">
        <q-list bordered class="rounded-borders">
          <q-expansion-item
            :model-value="true"
            expand-separator
            label="Scan"
          >
          <div class="inner">
            <q-select v-model="selectedScanner" :options="scanners" label="Selected Scanner" />
            <q-checkbox left-label v-model="showUI" label="Show UI" />
            <q-checkbox left-label v-model="feederEnabled" label="Auto Feeder" />
            <q-checkbox left-label v-model="duplexEnabled" label="Duplex" />
            <q-input
              v-model.number="resolution"
              type="number"
              style="max-width: 200px"
            >
            <template v-slot:before>
              <span style="font-size: 14px;color: black;">Resolution:</span>
            </template>
            </q-input>
            <div>
              <span>Pixel Type:</span>
              <q-radio v-model="pixelType" val="0" label="B&W" />
              <q-radio v-model="pixelType" val="1" label="Gray" />
              <q-radio v-model="pixelType" val="2" label="Color" />
            </div>
            
            <q-btn outline class="button" color="black" label="Scan" v-on:click="scan" />
          </div>
          </q-expansion-item>
          <q-expansion-item
            :model-value="true"
            expand-separator
            label="Save"
          >
            <div class="inner">
              <q-input outlined v-model="filename" label="Filename" />
              <q-btn outline class="button" color="black" label="Save as PDF" v-on:click="save" />
            </div>
          </q-expansion-item>
        </q-list>
      </div>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import DWT from 'components/DWT.vue';
import { WebTwain } from 'dwt/dist/types/WebTwain';
import { DeviceConfiguration } from 'dwt/dist/types/WebTwain.Acquire';
import { ref } from 'vue';
let DWObject:WebTwain|undefined;
const selectedScanner = ref("");
const showUI = ref(false);
const feederEnabled = ref(false);
const duplexEnabled = ref(false);
const resolution = ref(200);
const pixelType = ref(0);
const scanners = ref([] as string[]);
const filename = ref("Scanned");

const loadScanners = () => {
  if (DWObject) {
    let sourceNames = DWObject.GetSourceNames(false) as string[]; 
    scanners.value = sourceNames;
    if (sourceNames.length > 0 ) {
      selectedScanner.value = sourceNames[0];
    }
  }
}

const scan = () => {
  if (DWObject) {
    let selectedIndex = scanners.value.indexOf(selectedScanner.value);
    let deviceConfiguration:DeviceConfiguration = {};
      deviceConfiguration.IfShowUI = showUI.value;
      deviceConfiguration.IfFeederEnabled = feederEnabled.value;
      deviceConfiguration.IfDuplexEnabled = duplexEnabled.value;
      deviceConfiguration.SelectSourceByIndex = selectedIndex;
      deviceConfiguration.Resolution = resolution.value;
      deviceConfiguration.PixelType = pixelType.value;
      console.log(deviceConfiguration);
      DWObject.AcquireImage(deviceConfiguration);
  }
}

const save = () => {
  if (DWObject) {
    DWObject.SaveAllAsPDF(filename.value);
  }
}

const deleteSelected = () => {
  if (DWObject) {
    DWObject.RemoveAllSelectedImages();
  }
}

const edit = () => {
  if (DWObject) {
    let imageEditor = DWObject.Viewer.createImageEditor();
    imageEditor.show();
  }
}

const rotate = () => {
  if (DWObject) {
    DWObject.RotateRight(DWObject.CurrentImageIndexInBuffer);
  }
}

const moveUp = () => {
  if (DWObject) {
    DWObject.MoveImage(DWObject.CurrentImageIndexInBuffer,DWObject.CurrentImageIndexInBuffer-1);
  }
}

const moveDown = () => {
  if (DWObject) {
    DWObject.MoveImage(DWObject.CurrentImageIndexInBuffer,DWObject.CurrentImageIndexInBuffer+1);
  }
}

const onWebTWAINReady = (dwt:WebTwain) => {
  console.log("web twain ready");
  DWObject = dwt;
  console.log(DWObject);
  loadScanners();
};

</script>

<style>
.index {
  height: calc(100% - 50px);
}

.container {
  width: 100%;
  height: 100%;
  padding: 10px;
  position: absolute;
}

.inner {
  padding: 10px;
}

.button {
  margin-top: 10px;
}

#left {
  display: flex;
  height: 100%;
}

.toolbar {
  width: 50px;
  height: 100%;
  padding: 5px;
  font-size: 21px;
  border: 1px solid #d9d9d9;
  border-radius: 2px;
}

.dwt {
  width: calc(100% - 50px);
  height: 100%;
}

.toolbar-btn {
  width: 100%;
}

</style>