<template>
  <div>
    <q-select :model-value="value"
              :label="label"
              outlined
              dense
              options-dense
              use-input
              hide-selected
              fill-input
              input-debounce="500"
              :options="persons"
              option-value="id"
              option-label="name"
              map-options
              autocomplete="off"
              @filter="filterPersons"
              @update:model-value="changePerson"
              style="width: 100%">
      <template v-slot:no-option>
        <q-item>
          <q-item-section class="text-grey">
            Sin resultados
          </q-item-section>
        </q-item>
      </template>
      <!--    <template v-slot:before>-->
      <!--      &lt;!&ndash;      <q-icon name="fa-light fa-user-plus" @click="showDialogPerson = true" class="cursor-pointer"/>&ndash;&gt;-->
      <!--    </template>-->
      <template v-slot:after v-if="value">
        <q-icon name="fa-light fa-close" @click="clearPerson" class="cursor-pointer"/>
      </template>
    </q-select>
  </div>
</template>

<script>

import {onMounted, ref, toRef} from "vue"
import {usePosStore} from "stores/pos"
import _ from "lodash"
import {api} from "boot/axios"

export default {
  name: 'XSelectItem',
  props: {
    label: {
      default: '',
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
  emits: ['update:value', 'success'],
  setup(props, {emit}) {
    const handleInput = (value) => {
      emit("update:value", value)
    }

    let persons = ref([]);
    const filterPersons = async (val, update, abort) => {
      if (val.length < 2) {
        abort()
        return
      }
      let data = []
      await api.post(`/persons/options`, {
        'value': val,
        'type': 'customers',
        'document_type_id': '03',
      }).then(response => {
        data = response.data
      })
      update(() => {
          persons.value = data
        },
      )
    }

    const changePerson = (data) => {
      handleInput(data.id)
      emit('success', data)
    }

    const clearPerson = () => {
      handleInput(null)
    }

    return {
      persons,
      handleInput,
      filterPersons,
      changePerson,
      clearPerson
    }
  }
}
</script>
