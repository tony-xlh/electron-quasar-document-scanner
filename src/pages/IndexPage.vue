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
            label="Scan Settings"
          >
            Scan Settings
          </q-expansion-item>
          <q-expansion-item
            expand-separator
            label="Save"
          >
            Save
          </q-expansion-item>
        </q-list>
      </div>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import DWT from 'components/DWT.vue';
import { WebTwain } from 'dwt/dist/types/WebTwain';
let DWObject:WebTwain|undefined;
const onWebTWAINReady = (dwt:WebTwain) => {
  console.log("web twain ready");
  DWObject = dwt;
  console.log(DWObject);
  DWObject.AcquireImage();
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
</style>