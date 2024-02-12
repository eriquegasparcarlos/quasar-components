<template>
  <q-input v-model="valueInput"
           :label="label"
           outlined
           :autogrow="autogrow"
           :counter="counter"
           :maxlength="maxLength"
           :input-class="inputClass"
           :disable="disable"
           :readonly="readonly"
           :error="!!error"
           :no-error-icon="true"
           :dense="dense"
           :type="(type==='password')?(showPwd?'text':'password'):type"
           :hint="hint"
           :mask="mask"
           :fill-mask="fillMask===''?false:fillMask"
           :reverse-fill-mask="reverseFillMask"
           class="x-input"
           @update:model-value="handleInput">
    <template v-slot:prepend v-if="slotPrepend">
      <slot name="prepend"></slot>
    </template>
    <template v-slot:before v-if="slotBefore">
      <slot name="before"></slot>
    </template>
    <template v-slot:append v-if="slotAppend">
      <slot name="append"></slot>
    </template>
    <template v-slot:after v-if="slotAfter">
      <slot name="after"></slot>
    </template>
    <template v-slot:append v-if="type === 'password'">
      <q-icon :name="showPwd ? 'fa-light fa-eye-slash' : 'fa-light fa-eye'"
              size="xs"
              class="cursor-pointer"
              @click="showPwd = !showPwd"
      />
    </template>
  </q-input>
</template>

<script>
import {ref} from "vue";

export default {
  name: 'XInput',
  props: {
    label: {
      type: String,
      default: null,
    },
    value: {
      default: ''
    },
    dense: {
      type: Boolean,
      default: true
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
    autogrow: {
      type: Boolean,
      default: false,
    },
    counter: {
      type: Boolean,
      default: false,
    },
    maxLength: {
      default: null
    },
    hint: {
      default: null,
    },
    mask: {
      default: '',
    },
    fillMask: {
      default: false,
    },
    reverseFillMask: {
      type: Boolean,
      default: false
    },
    slotPrepend: {
      type: Boolean,
      default: false,
    },
    slotBefore: {
      type: Boolean,
      default: false,
    },
    slotAppend: {
      type: Boolean,
      default: false,
    },
    slotAfter: {
      type: Boolean,
      default: false,
    },
    error: {
      default: null,
    },
    type: {
      default: 'text',
    },
  },
  setup(props, {emit}) {
    let valueInput = props.value;
    const handleInput = (value) => {
      emit("update:value", value);
    };

    return {
      valueInput,
      showPwd: ref(false),
      handleInput,
    }
  }
}
</script>
