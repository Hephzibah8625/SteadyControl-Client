<template>

  <div v-if="nodeData.children && nodeData.children.length" class="node" >
    <h3  class="node_text" :style="'width:' + ((100/columns) * (100/width)) + '%'">{{nodeData.name}}</h3>

    <div class="node_children" :style="'width: ' + (100 - ((100/columns) * (100/width))) + '%'">
      <node v-for="child in nodeData.children"
            :node-data="child"
            :width="(width - (100/columns))"
            :columns="columns"
            :city-data="cityData"/>
    </div>
  </div>

  <div v-else class="node tooltip">
    <h3 class="node_text" style="width: 100%">{{nodeData.name}}</h3>
    <span class="tooltip_text">{{ cityData[nodeData.city_id] }}</span>
  </div>
</template>

<script>
export default {
  name: "Node",
  props: {
    nodeData: Object,
    width: Number,
    cityData: Object,
    columns: Number
  }
}
</script>

<style scoped>

.node {
  width: 100%;
  display: flex;
}

.node_text {
  border-right: 1px solid #6f6f6f;
  border-bottom: 1px solid #6f6f6f;
  padding: 15px 0;
  text-align: center;
}

.tooltip {
  position: relative;
}

.tooltip .tooltip_text {
  visibility: hidden;
  background-color: black;
  color: #fff;
  text-align: center;
  width: 120px;
  padding: 5px 0;
  border-radius: 6px;

  position: absolute;
  z-index: 1;

  bottom: 65%;
  left: 50%;
  margin-left: -60px;
}

.tooltip:hover .tooltip_text {
  visibility: visible;
}


</style>