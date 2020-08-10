<template>
  <div class="search__filter-option">
    <li v-for="(option, i) in options || carrier" :key="i" v-on:click="$emit('filter', option.carrier || option.id)">
      <input type="checkbox" :checked="option.status" />
      <p>{{option.title}}</p>
    </li>
  </div>
</template>

<script>
export default {
  props: {
    options: {
      type: Array
    },
    carrier: {
      type: Array
    }
  }
};
</script>

<style scoped>
.search__filter-option {
  display: flex;
  flex-direction: column;
  overflow: auto;
  white-space: nowrap;
  max-height: 310px;
  -ms-overflow-style: none;
}
.search__filter-option::-webkit-scrollbar {
  width: 2px;
  position: absolute;
}
.search__filter-option::-webkit-scrollbar-thumb {
  -webkit-border-radius: 2px;
  border-radius: 2px;
  background: #e1e1e1;

}
li {
  display: flex;
  align-items: center;
}
li:hover {
  background: #ebebeb;
  cursor: pointer;
}
p {
  font-size: 12px;
  line-height: 16px;
  margin: 12px;
}
</style>
