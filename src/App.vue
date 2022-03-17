<template>
  <main>

    <div class="tags">
      <div v-for="type in types" class="tags_item" :style="'width: ' + ( 100 / (types.length + 1) ) + '%'">
        <h2>{{ type.toUpperCase() }}</h2>
      </div>
      <div class="tags_item" :style="'width: ' + ( 100 / (types.length + 1) ) + '%'">
        <h2>USER</h2>
      </div>
    </div>

    <div class="table">
      <div v-for="children of rootObj">
        <tree :tree-data="children" :city-data="mapCityData" :columns="(types.length + 1)"></tree>
      </div>
    </div>

  </main>
</template>



<script>
import Tree from './components/Tree.vue';

export default {
  name: "App",
  components: {Tree},

  data() {
    return {
      cities: [{ id: 0, name: '', data: '' }],
      citizens: [{ id: 0, name: '', city_id: 0, groups: [{ type: '', name: ''}] }],
      mapCityData: {},
      rootObj: [],
      types: [],
    }
  },

  mounted() {
    this.getCities();
    this.getCitizens();
  },

  methods: {

    async getCities() {
      const response = await fetch('http://localhost:3000/cities');
      this.cities = await response.json();

      this.cities.forEach(item => this.mapCityData[item.id] = item.data );
    },

    async getCitizens() {
      const response = await fetch('http://localhost:3000/citizens');
      this.citizens = await response.json();

      this.citizens[0].groups.forEach(item => {
        this.types.push(item.type);
      })


      this.citizens.sort((a, b) => {
        for (let i = 0; i < a.groups.length; i += 1) {
          if (a.groups[i].name > b.groups[i].name) return 1;
          if (a.groups[i].name < b.groups[i].name) return -1;
        }
        return 1;
      })

      this.buildTable(0, this.citizens.length, 0, this.rootObj);
    },

    // Рекурсия для постройки дерева, где на каждом уровне уникальные "groups[i]"
    buildTable(start, end, index, prev) {

      if (index === this.types.length) {
        for (let i = start; i < end; i += 1) {
          prev.push({name: this.citizens[i].name, city_id: this.citizens[i].city_id});
        }
        return;
      }

      const indexes = [start];
      for (let i = start + 1; i < end; i += 1) {
        if (this.citizens[i].groups[index].name !== this.citizens[i - 1].groups[index].name) {
          indexes.push(i);
        }
      }

      // Новое развлетвление для каждого отличающегося 'group'
      for (let i = 0; i < indexes.length - 1; i += 1) {
        prev[prev.length] = {name: this.citizens[indexes[i]].groups[index].name, children: []};
        this.buildTable(indexes[i], indexes[i + 1], index + 1, prev[prev.length - 1].children);
      }

      prev[prev.length] = {name: this.citizens[indexes[indexes.length - 1]].groups[index].name, children: []};
      this.buildTable(indexes[indexes.length - 1], end, index + 1, prev[prev.length - 1].children);
    }
  },
}
</script>

<style>

* {
  padding: 0;
  margin: 0;
}

main {
  max-width: 1500px;
  width: 100%;
  margin: auto;
  padding: 2em;
}

.tags {
  width: 100%;
  display: flex;
  flex-direction: row;
  align-items: center;
  border: 1px solid #6f6f6f;
  margin-bottom: 10px;
}

.tags .tags_item {
  text-align: center;
  padding: 7px 0;
  border-right: 1px solid #6f6f6f;
}

.table {
  width: 100%;
}
</style>
