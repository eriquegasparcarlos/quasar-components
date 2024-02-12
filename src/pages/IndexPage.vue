<template>
  <q-page class="q-px-lg q-pt-lg q-pb-none">
    <div class="row">
      <div class="col-1">
        <x-input-quantity v-model:value="input"></x-input-quantity>
        valor: {{ input }}
      </div>
    </div>
  </q-page>
</template>

<script setup>
import {ref} from "vue"
import XInputQuantity from "components/XInputQuantity.vue"
const input = ref(1)

</script>
