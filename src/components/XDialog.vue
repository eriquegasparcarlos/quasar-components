<template>
  <q-dialog :model-value="openDialog"
            persistent
            transition-show="scale"
            transition-hide="scale"
            position="top"
            class="x-dialog"
            @before-show="beforeShow">
    <q-card :style="`max-width: ${width}; width: ${width};`" class="x-dialog-card">
      <q-card-section class="x-dialog-card-section-title">
        <div class="title">{{ title }}</div>
        <q-space/>
        <q-btn icon="fa-light fa-close" flat dense @click="clickCloseDialog" v-if="showButtonClose"/>
      </q-card-section>
      <q-card-section class="x-dialog-card-section-content">
        <div class="row q-col-gutter-md">
          <slot name="content"></slot>
        </div>
      </q-card-section>
      <q-card-section class="x-dialog-card-section-footer">
        <div class="row">
          <slot name="footer"></slot>
        </div>
      </q-card-section>
      <x-loading :loading="loading"></x-loading>
    </q-card>
  </q-dialog>
</template>

<script>

import XLoading from "components/XLoading.vue";
import {toRef} from "vue";

export default {
  name: "XDialog",
  components: {XLoading},
  props: {
    openDialog: {
      type: Boolean,
      required: false,
    },
    title: {
      type: String,
      required: true,
    },
    loading: {
      type: Boolean,
      default: false,
    },
    width: {
      type: String,
      default: '600px'
    },
    showButtonClose: {
      type: Boolean,
      default: true,
    },
  },
  emits: ['beforeShow', 'closeDialog'],
  setup(props, {emit}) {
    const beforeShow = () => {
      emit('beforeShow')
    }

    const clickCloseDialog = () => {
      emit('closeDialog')
    }

    return {
      beforeShow,
      clickCloseDialog
    }
  }
}
</script>
