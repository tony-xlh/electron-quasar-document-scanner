<template>
  <div ref="viewer" id="dwtcontrolContainer"></div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import Dynamsoft from 'dwt';

const props = defineProps(['width','height','license']);
const emit = defineEmits(['onWebTWAINReady']);
const initialized = ref(false);
const viewer = ref(null);
const ContainerId = "dwtcontrolContainer";
let DWObject;

const initDWT = () => {
  Dynamsoft.DWT.RegisterEvent('OnWebTwainReady', () => {
    DWObject = Dynamsoft.DWT.GetWebTwain(ContainerId);
    if (viewer.value) {
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


</script>
