<template>
  <div class="tree">
    <node :nodeData="treeData" :width="100" :cityData="cityData" :columns="columns"></node>
  </div>
</template>

<script>
import Node from "./Node.vue";

export default {
  name: "Tree",
  components: {Node},
  props: {
    treeData: Object,
    cityData: Object,
    columns: Number
  }
}
</script>

<style scoped>

.tree {
  border: 1px solid #6f6f6f;
  width: 100%;
  margin-bottom: 10px;
}

</style>