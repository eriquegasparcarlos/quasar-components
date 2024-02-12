<template>
  <q-input v-model="value"
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
  </q-input>
</template>

<style scoped>
.x-input.q-field--with-bottom {
  padding-bottom: 0;
}
</style>

<script>
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
    }
  },
  setup(props, {emit}) {
    const handleInput = (value) => {
      emit("update:value", value);
    };

    return {
      handleInput,
    }
  }
}
</script>
