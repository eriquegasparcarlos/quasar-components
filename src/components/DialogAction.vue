<template>
  <x-dialog v-model:open-dialog="openDialog"
            :title="title"
            :loading="loading"
            :show-button-close="false"
            width="520px"
            @close-dialog="closeDialog"
            @before-show="handleOpen">
    <template v-slot:content>
      <div class="col-24">{{ text }}</div>
      <div class="col-24">
        <x-input label="Contraseña"
                 type="password"
                 v-model="form.user_password"
                 :error="errors.user_password">
        </x-input>
      </div>
      <div class="col-24" v-if="showVoidedDescription">
        <x-input label="Motivo de anulación"
                 v-model="form.voided_description"
                 :error="errors.voided_description">
        </x-input>
      </div>
    </template>
    <template v-slot:footer>
      <div class="col-24 text-right">
        <x-button flat :color="color" @click="closeDialog" label="Cancelar"></x-button>
        <x-button unelevated :color="color" @click="onSubmit" :loading="loading" :label="buttonLabel"></x-button>
      </div>
    </template>
  </x-dialog>
</template>

<script>

import XLoading from "components/XLoading.vue";
import {ref, toRef} from "vue";
import XButton from "components/XButton.vue";
import {api} from "boot/axios";
import {useQuasar} from "quasar";
import XDialog from "components/XDialog.vue";
import XInput from "components/XInput.vue";

export default {
  name: "DialogAction",
  components: {XInput, XDialog, XButton, XLoading},
  props: {
    showDialog: {
      type: Boolean,
      required: false,
    },
    resource: {
      type: String,
      required: true,
    },
    recordId: {
      required: true,
    },
    action: {
      type: String,
      default: 'delete',
    }
  },
  emits: ['update:showDialog', 'success'],
  setup(props, {emit}) {
    const $q = useQuasar()
    const openDialog = toRef(props, 'showDialog')
    let loading = ref(false)
    let title = ref('')
    let form = ref({})
    let errors = ref({})
    let color = ref('red')
    let buttonLabel = ref('Eliminar')
    let text = ref('Por favor ingresar su contraseña para eliminar el registro.')
    let showVoidedDescription = ref(false)

    const initForm = () => {
      errors.value = {}
      form.value = {
        id: props.recordId,
        user_password: null,
        voided_description: null,
      }
      showVoidedDescription.value = false
      if (props.action === 'default') {
        color.value = 'primary'
        text.value = 'Por favor ingresar su contraseña para predeterminar el registro.'
        buttonLabel.value = 'Aceptar'
      }
      if (props.action === 'active') {
        color.value = 'primary'
        text.value = 'Por favor ingresar su contraseña para activar/desactivar el registro.'
        buttonLabel.value = 'Aceptar'
      }
      if (props.action === 'voided') {
        text.value = 'Por favor ingresar su contraseña y el motivo de anulación para anular el registro.'
        buttonLabel.value = 'Anular'
        showVoidedDescription.value = true
      }
      if (props.action === 'duplicate') {
        text.value = 'Por favor ingresar su contraseña para duplicar el registro.'
        buttonLabel.value = 'Duplicar'
      }
    };

    const handleOpen = () => {
      initForm();
      title.value = 'Confirmar'
    };

    const onSubmit = async () => {
      loading.value = true
      await api.post(`/${props.resource}/${props.action}`, form.value)
        .then(response => {
          let data = response.data;
          if (data.success) {
            $q.notify({type: 'positive', icon: 'fa-light fa-check', message: data.message});
            emit('success')
            closeDialog()
          } else {
            $q.notify({type: 'negative', icon: 'fa-light fa-xmark', message: data.message});
          }
        })
        .catch(() => {

        })
        .finally(() => {
          loading.value = false
        });
    }

    const closeDialog = () => {
      emit('update:showDialog', false);
    };

    return {
      openDialog,
      loading,
      title,
      errors,
      form,
      color,
      buttonLabel,
      text,
      showVoidedDescription,
      handleOpen,
      onSubmit,
      closeDialog,
    }
  }
}
</script>
