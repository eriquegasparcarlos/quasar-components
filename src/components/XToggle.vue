<template>
  <q-toggle v-model="valueInput"
            :label="label"
            dense
            @update:model-value="handleInput"></q-toggle>
</template>

<script>
import {ref} from "vue";

export default {
  name: 'XToggle',
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
  },
  setup(props, {emit}) {
    let valueInput = props.value;
    const handleInput = (value) => {
      emit("update:value", value);
    };

    return {
      valueInput,
      handleInput,
    }
  }
}
</script>
