<template>
  <div class="x-input-quantity">
    <q-input v-model="valInput"
             :label="label"
             :name="name"
             dense
             outlined
             :maxlength="maxLength"
             input-class="text-center"
             :no-error-icon="true"
             :readonly="readonly"
             @update:model-value="handleInput"
             class="x-input-quantity-no-label">
      <template v-slot:before>
        <q-btn class="full-height" dense unelevated size="md" icon="fal fa-minus" @click.prevent="decrease"/>
      </template>
      <template v-slot:after>
        <q-btn class="full-height" dense unelevated size="md" icon="fal fa-plus" @click.prevent="increase"/>
      </template>
    </q-input>
  </div>
</template>

<script setup>
import {toRef} from "vue";

const props = defineProps({
  value: {
    default: ''
  },
  name: {
    type: String,
    default: '',
  },
  label: {
    type: String,
    default: null,
  },
  inputClass: {
    type: String,
    default: null,
  },
  disable: {
    type: Boolean,
    default: false
  },
  readonly: {
    type: Boolean,
    default: false
  },
  maxLength: {
    default: null
  },
  error: {
    default: null,
  }
})
const emit = defineEmits(['update:value'])

const valInput = toRef(props, 'value');
const handleInput = (value) => {
  if (value <= 0) {
    value = 1;
  }
  emit("update:value", value);
};

const increase = () => {
  let new_value = parseFloat(props.value) + 1;
  emit("update:value", new_value);
};

const decrease = () => {
  let new_value = parseFloat(props.value) - 1;
  if (new_value <= 0) {
    new_value = props.value;
  }
  emit("update:value", new_value);
}
</script>
