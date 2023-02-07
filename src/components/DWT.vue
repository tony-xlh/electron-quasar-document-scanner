<template>
  <div ref="viewer" id="dwtcontrolContainer"></div>
</template>

<script setup lang="ts">
import { onMounted, ref, watch } from 'vue';
import Dynamsoft from 'dwt';
import { WebTwain } from 'dwt/dist/types/WebTwain';

const props = defineProps(['width','height','cols','rows','license']);
const emit = defineEmits(['onWebTWAINReady']);
const initialized = ref(false);
const viewer = ref(null);
const ContainerId = "dwtcontrolContainer";
let DWObject:WebTwain|undefined;

const initDWT = () => {
  Dynamsoft.DWT.RegisterEvent('OnWebTwainReady', () => {
    DWObject = Dynamsoft.DWT.GetWebTwain(ContainerId);
    resizeViewer();
    updateViewMode();
    emit("onWebTWAINReady",DWObject);
  });
  if (props.license) {
    Dynamsoft.DWT.ProductKey = props.license;
  }
  Dynamsoft.DWT.ResourcesPath = "assets/dwt-resources";
  Dynamsoft.DWT.Containers = [{
      WebTwainId: 'dwtObject',
      ContainerId: ContainerId
  }];
  Dynamsoft.DWT.Load();
}

onMounted(async () => {
  console.log("mounted");
  console.log(viewer.value);
  initDWT();
});

const resizeViewer = () => {
  if (viewer.value && DWObject) {
    let ele = viewer.value as HTMLElement;
    if (props.width) {
      console.log("set width: "+props.width);
      DWObject.Viewer.width = props.width;
      ele.style.width = props.width;
    }
    if (props.height) {
      DWObject.Viewer.height = props.height;
      ele.style.height = props.height;
    }
  }
}

const updateViewMode = () => {
  if (props.cols && props.rows && DWObject) {
    DWObject.Viewer.setViewMode(parseInt(props.cols),parseInt(props.rows));
  }
}

watch(() => [props.width,props.height], ([newWidth,oldWidth], [newHeight,oldHeight]) => {
  resizeViewer();
});

watch(() => [props.cols,props.rows], ([newCols,oldCols], [newRows,oldRows]) => {
  updateViewMode();
});


</script>
