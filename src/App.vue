<script setup>

import { ref, computed } from 'vue'

const header = ref('Shopping List App')
const editing = ref(false)
const items = ref([])

const newItem = ref("")
const dontForget = ref("false")
let warning = ref("")

const sortedItems = computed(() => {
  const forgetItems = items.value.filter(item => item.dontForget);
  const nonForgetItems = items.value.filter(item => !item.dontForget);
  return [...forgetItems, ...nonForgetItems];
});

const characterCount = computed(() => {
  return newItem.value.length 
})

const saveItem = () => {
  const newItemLower = newItem.value.toLowerCase();
  if (!items.value.some(item => item.label.toLowerCase() === newItemLower)) {
    items.value.push({
      id: items.value.length + 1,
      label: newItem.value,
      dontForget: dontForget.value,
    });
    warning.value = "";
  } else {
    warning.value = 'This item is already on the list';
    return;
  }

  newItem.value = "";
  dontForget.value = "";
}

const doEdit = (e) => {
  editing.value = e
  newItem.value = ""
  dontForget.value = ""
}

const togglePurchased = (item) => {
  item.purchased = !item.purchased
}

const remaining = computed(() => {
  return items.value.filter(item => !item.purchased).length;
});

</script>

<!-- ---------------------------------------- -->

<template>

  <div class="container">

    <div class="header">
      <h1>{{ header }}</h1>
      <button v-if="editing" class="btn" @click="doEdit(false)">Cancel</button>
      <button v-else class="btn btn-primary" @click="doEdit(true)">Add product</button>
    </div>
    
    <form v-if="editing" @submit.prevent="saveItem">

      <div class="input-checkbox">
        <div class="inputfield">
          <input v-model="newItem" type="text" placeholder="Add a product">
          <p>{{ warning }}</p>
          <p class="counter">{{characterCount}}/20</p>
        </div>

        <label class="checkbox"><input type="checkbox" v-model="dontForget">Don't forget this item!</label>
      </div>

      <button :disabled="newItem.length <= 1 || newItem.length > 20" class="btn btn-primary">Save product</button>

    </form>

    <ul>
      <li 
        v-for="(item, index) in sortedItems" 
        @click="togglePurchased(item)"
        :key="item.id"
        :class="{strikeout: item.purchased, forget: item.dontForget}"
      >
        {{item.label}}
      </li>
    </ul>

    <p v-if="!items.length">No products on your list</p>

    <span class="items-count">
      <strong>{{ remaining }}</strong>
      <span>{{ remaining === 1 ? ' item' : ' items' }} left</span>
    </span>

  </div>

</template>