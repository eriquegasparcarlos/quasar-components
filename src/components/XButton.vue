<template>
  <q-btn no-caps
         :outline="outline"
         :unelevated="unelevated"
         :flat="flat"
         :color="color"
         :class="`${myClass} q-ml-sm`"
         :disable="loading"
         @click="clickButton">{{ label }}</q-btn>
</template>

<script>

export default {
  name: "XButton",
  emits: ['click'],
  props: {
    label: {
      type: String,
      default: 'Aceptar',
    },
    outline: {
      type: Boolean,
      default: false,
    },
    unelevated: {
      type: Boolean,
      default: false,
    },
    flat: {
      type: Boolean,
      default: false,
    },
    primary: {
      type: Boolean,
      default: false,
    },
    color: {
      type: String,
      default: '',
    },
    myClass: {
      type: String,
      default: ''
    },
    loading: {
      type: Boolean,
      default: false
    }
  },
  setup(props, {emit}) {
    const clickButton = () => {
      emit("click");
    };

    return {
      clickButton,
    }
  }
}
</script>
