<template>
  <q-select v-model="valInput"
            outlined
            dense
            use-input
            hide-selected
            fill-input
            input-debounce="0"
            :label="label"
            :options="filter_locations"
            option-value="id"
            option-label="name"
            emit-value
            map-options
            options-dense
            @update:model-value="handleInput"
            @filter="filterLocation">
    <template v-slot:no-option>
      <q-item>
        <q-item-section class="text-grey">
          Sin resultados
        </q-item-section>
      </q-item>
    </template>
  </q-select>
</template>

<script>

import {onMounted, ref, toRef} from "vue";
import {usePosStore} from "stores/pos";
import _ from "lodash";

export default {
  name: 'XSelectLocation',
  props: {
    label: {
      default: 'Ubigeo',
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
    error: {
      default: null,
    },
  },
  setup(props, {emit}) {
    const posStore = usePosStore();

    let filter_locations = ref(posStore.locations);

    const valInput = toRef(props, 'value');
    const handleInput = (value) => {
      emit("update:value", value);
    };

    const filterLocation = (val, update, abort) => {
      update(() => {
        const needle = _.lowerCase(val);
        filter_locations.value = _.filter(posStore.locations, v => _.lowerCase(v.name).indexOf(needle) > -1);
      })
    }

    return {
      filter_locations,
      valInput,
      handleInput,
      filterLocation,
    }
  }
}
</script>
