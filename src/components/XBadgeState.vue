<template>
  <q-badge :style="{'background-color':  `${color}!important`}" :label="name" v-if="id"></q-badge>
</template>

<script>

export default {
  name: "XBadgeState",
  props: {
    id: {
      type: String,
      default: '01',
      required: true,
    },
    name: {
      type: String,
      required: true,
    },
    color: {
      type: String,
      required: true,
    },
  }
}
</script>
