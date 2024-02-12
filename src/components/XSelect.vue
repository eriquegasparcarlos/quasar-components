<template>
  <q-select :model-value="value"
            :label="label"
            :readonly="readonly"
            :disable="disable"
            :clearable="clearable"
            outlined
            :dense="dense"
            :multiple="multiple"
            options-dense
            :options="options"
            :option-value="optionValue"
            :option-label="optionLabel"
            emit-value
            map-options
            :error="!!error"
            :no-error-icon="true"
            :class="`x-select select-truncate ${myClass}`"
            @update:model-value="value = $event">
    <template v-slot:after v-if="showButtonNew">
      <q-btn size="sm" round flat icon="fal fa-plus" @click.prevent="$emit('click-new')">
      </q-btn>
    </template>
  </q-select>
</template>

<script>

export default {
  name: 'XSelect',
  props: {
    label: {
      type: String,
    },
    value: {
      default: ''
    },
    dense: {
      type: Boolean,
      default: true
    },
    disable: {
      type: Boolean,
      default: false
    },
    clearable: {
      type: Boolean,
      default: false
    },
    readonly: {
      type: Boolean,
      default: false
    },
    multiple: {
      type: Boolean,
      default: false
    },
    options: {
      default: [],
    },
    optionValue: {
      type: String,
      default: 'id'
    },
    optionLabel: {
      type: String,
      default: 'name'
    },
    error: {
      default: null,
    },
    showButtonNew: {
      type: Boolean,
      default: false
    },
    myClass: {
      type: String,
      default: ''
    }
  }
}
</script>
