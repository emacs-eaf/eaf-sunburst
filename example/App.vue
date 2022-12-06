<template>
  <div id="app" class="container-fluid">
    <div class="card-body father">
      <sunburst
        id="resizable"
        class="sunburst"
        :data="data"
        :max-label-text="20"
        :centralCircleRelativeSize="centralCircleRelativeSize"
        :showLabels="showLabels"
        :minAngleDisplayed="minAngleDisplayed"
        :colorScheme="colorScheme"
        :colorScale="colorScale"
        :inAnimationDuration="inAnimationDuration"
        :outAnimationDuration="outAnimationDuration">
        <breadcrumbTrail
          slot="legend"
          slot-scope="{ nodes, colorGetter, width }"
          :current="nodes.mouseOver"
          :root="nodes.root"
          :colorGetter="colorGetter"
          :from="nodes.zoomed"
          :width="width"
        />

        <nodeInfoDisplayer
          slot="top"
          slot-scope="{ nodes }"
          :current="nodes.mouseOver"
          :root="nodes.root"
          :clicked="nodes.clicked"
          description="of selected"
        />

        <template slot="pop-up" slot-scope="{ data }">
          <div class="pop-up">{{data.name}}</div>
        </template>

        <template slot-scope="{ on, actions }">
          <highlightOnHover v-bind="{ on, actions }" />
          <zoomOnClick v-bind="{ on, actions }" />
          <popUpOnHover  v-bind="{ on, actions }"/>
        </template>
      </sunburst>
    </div>
  </div>
</template>

<script>
 import sunburst from "@/components/sunburst";
 import nodeInfoDisplayer from "@/components/nodeInfoDisplayer";
 import breadcrumbTrail from "@/components/breadcrumbTrail";
 //behaviours
 import highlightOnHover from "@/components/behavior/highlightOnHover";
 import zoomOnClick from "@/components/behavior/zoomOnClick";
 import popUpOnHover from "@/components/behavior/popUpOnHover";

 import { QWebChannel } from "qwebchannel";
 
 import { colorSchemes } from "@/infra/colorSchemes";
 import data from "../data/data";
 import { scaleOrdinal } from "d3-scale";

 const colorSchemesNames = Object.keys(colorSchemes).map(key => ({
   value: key,
   text: colorSchemes[key].name
 }));

 
 export default {
   name: "app",
   data() {
     return {
       data,
       minAngleDisplayed: 0.01,
       colorScheme: colorSchemesNames[0].value,
       colorSchemes: colorSchemesNames,
       inAnimationDuration: 100,
       outAnimationDuration: 1000,
       overrideColorScale: false,
       centralCircleRelativeSize: 25,
       showLabels: false,
       custoColorScale: scaleOrdinal([
         "#e39b89",
         "#31ea74",
         "#3c7227",
         "#9dad1f"
       ])
     };
   },
   computed: {
     colorScale() {
       return this.overrideColorScale ? this.custoColorScale : null;
     }
   },
   methods: {
     init(data) {
       this.data = data;
     },
     
     showLabelsFunction(d) {
       const {
         data,
         context: { angle, relativeDepth }
       } = d;
       if (relativeDepth > 2 || angle < 5) {
         return null;
       }
       return data.name;
     }
   },
   created() {
     // eslint-disable-next-line no-undef
     new QWebChannel(qt.webChannelTransport, channel => {
       window.pyobject = channel.objects.pyobject;
       this.pyobject = window.pyobject;
     });
   },
   mounted() {
     window.init = this.init;
   },
   components: {
     sunburst,
     nodeInfoDisplayer,
     breadcrumbTrail,
     highlightOnHover,
     zoomOnClick,
     popUpOnHover
   }
 };
</script>

<style lang="less">
 body {
   width: 100%;
   height: 100%;
 }

 #app {
   font-family: "Avenir", Helvetica, Arial, sans-serif;
   -webkit-font-smoothing: antialiased;
   -moz-osx-font-smoothing: grayscale;
   text-align: center;
   
   height: 800px;
   
   padding: 0;

   .pop-up {
     background-color: white;
     border: black;
     pointer-events: none;
     opacity: 0.92;
   }

   .main-row {
     height: 800px;
   }

   .control-middle {
     height: 600px;
   }

   .resizable {
     margin-left: calc(50% - 50px);
   }

   .father {
     position: absolute;
     width: 100%;
     height: 100%;
     display: flex;
     justify-content: center;
   }

   .sunburst {
     width: 100%;
     height: 100%;
     position: relative;
   }

   .custo-checkbox {
     display: flex;
     justify-content: space-between;
   }
 }
</style>
