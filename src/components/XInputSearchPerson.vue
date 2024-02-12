<template>
  <q-input :model-value="value"
           label="Número documento"
           dense
           outlined
           :readonly="readonly"
           :counter="counter"
           :maxlength="maxLength"
           :disable="loading"
           :mask="mask"
           class="x-input-search-person"
           @update:model-value="handleInput">
    <template v-slot:after>
      <q-btn size="sm"
             outline
             color="primary"
             icon="fa-light fa-magnifying-glass"
             style="height: 100%"
             :loading="loading"
             :disable="!loading && value && (value.length < maxLength)"
             @click.prevent="clickSearch(value)"></q-btn>
    </template>
  </q-input>
</template>

<script type="text/javascript">
import {onMounted, ref, toRef, watch} from "vue"
import {api} from 'boot/axios'
import {useQuasar} from "quasar"

export default {
  name: 'XInputSearchPerson',
  props: {
    identityDocumentTypeId: {
      required: true,
      type: String,
      default: ''
    },
    value: {
      type: String,
      default: ''
    },
    readonly: {
      type: Boolean,
      default: false
    },
  },
  emits: ['update:value', 'success'],
  setup(props, {emit}) {
    const $q = useQuasar()
    let loading = ref(false)
    let maxLength = ref(20)
    let counter = ref(false)
    let mask = ref('')
    let identityDocumentTypeId = toRef(props, 'identityDocumentTypeId')

    watch(identityDocumentTypeId, () => {
      changeIdentityDocumentTypeId()
    })

    const handleInput = (value) => {
      emit("update:value", value)
    }

    const changeIdentityDocumentTypeId = () => {
      counter.value = false
      maxLength.value = 20
      mask.value = ''
      if (props.identityDocumentTypeId === '6') {
        counter.value = true
        maxLength.value = 11
        mask.value = '###########'
      }
      if (props.identityDocumentTypeId === '1') {
        counter.value = true
        maxLength.value = 8
        mask.value = '########'
      }
    }

    const clickSearch = async (number) => {
      loading.value = true
      await api.post(`/store/person_search`, {
        'identity_document_type_id': props.identityDocumentTypeId,
        'number': number,
      })
        .then(response => {
          let res = response.data
          if (res.success) {
            emit('success', res.data)
          } else {
            $q.notify({type: 'negative', icon: 'fa-light fa-xmark', message: res.message})
          }
        }).finally(() => {
          loading.value = false
        })
    }

    onMounted(() => {
      changeIdentityDocumentTypeId()
    })

    return {
      loading,
      maxLength,
      counter,
      mask,
      handleInput,
      changeIdentityDocumentTypeId,
      clickSearch
    }
  }
}
</script>
