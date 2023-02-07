<template>
  <q-page class="index">
    <div class="row container">
      <div class="col-8 left">
        <DWT width="100%" height="100%" @onWebTWAINReady="onWebTWAINReady"></DWT>
      </div>
      <div class="col-4 right">
        <q-list bordered class="rounded-borders">
          <q-expansion-item
            expand-separator
            label="Scan"
          >
          <div class="inner">
            <q-select v-model="selectedScanner" :options="scanners" label="Selected Scanner" />
            <q-btn color="primary" label="Scan" v-on:click="scan" />
          </div>
          </q-expansion-item>
          <q-expansion-item
            expand-separator
            label="Save"
          >
            <div class="inner">
              Save
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

</style>