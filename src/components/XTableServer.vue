<template>
  <q-card flat style="margin-top: 0;" class="x-table-card">
    <q-card-section class="x-table-section-title q-mb-md" v-if="showSectionTitle">
      <div class="flex items-center">
        <div class="col title">{{ title }}</div>
        <div class="col-shrink flex items-center">
          <slot name="button-actions"></slot>
          <q-btn outline
                 icon="fa-light fa-plus"
                 color="primary"
                 no-caps
                 @click.prevent="clickCreate()"
                 v-if="showButtonNew"><span class="q-ml-sm">Nuevo</span>
          </q-btn>
          <slot name="extra-buttons"></slot>
          <q-btn outline
                 no-caps
                 color="blue"
                 class="q-ml-sm"
                 icon="fa-light fa-download"
                 v-if="showButtonExport"><span class="q-ml-sm">Exportar</span>
            <q-menu dense auto-close>
              <q-list style="min-width: 100px" dense>
                <q-item clickable @click.prevent="clickExport('pdf')">
                  <q-item-section>PDF</q-item-section>
                </q-item>
                <q-item clickable @click.prevent="clickExport('xls')">
                  <q-item-section>Excel</q-item-section>
                </q-item>
              </q-list>
            </q-menu>
          </q-btn>
          <q-btn outline
                 no-caps
                 class="q-ml-sm"
                 icon="fa-light fa-columns-3"
                 v-if="showButtonColumns && selectColumns.length > 0"><span class="q-ml-sm">Columnas</span>
            <q-menu class="q-pa-sm">
              <q-option-group :options="selectColumns"
                              type="checkbox"
                              v-model="selectVisibleColumns"
                              @update:model-value="changeSelectVisibleColumns"></q-option-group>
            </q-menu>
          </q-btn>
        </div>
      </div>
    </q-card-section>
    <q-card-section v-if="showFilter" class="x-table-section-filters">
      <div class="row q-col-gutter-sm">
        <template v-for="f in filters">
          <div :class="f.class" v-if="f.type === 'select'">
            <q-select v-model="f.value"
                      :label="f.label"
                      :options="f.options"
                      option-value="id"
                      option-label="name"
                      emit-value
                      map-options
                      outlined
                      dense
                      options-dense
                      :multiple="f.multiple"
                      :clearable="f.clearable"
                      class="select-truncate"
                      @update:model-value="changeFilter"></q-select>
          </div>
          <div :class="f.class" v-if="f.field === 'period'">
            <div class="row q-col-gutter-x-sm q-col-gutter-y-sm">
              <div class="col">
                <q-select v-model="f.value"
                          :label="f.label"
                          :options="f.options"
                          option-value="id"
                          option-label="name"
                          emit-value
                          map-options
                          outlined
                          dense
                          options-dense
                          class="select-truncate"
                          @update:model-value="changeFilter"></q-select>
              </div>
              <template v-if="f.value === 'date' || f.value === 'between_dates'">
                <div class="col-24 col-sm-8">
                  <q-field label="Fecha del" outlined dense stack-label class="x-datepicker">
                    <template v-slot:control>
                      <datepicker v-model="f.date_start"
                                  format="dd/MM/yyyy"
                                  modelType="yyyy-MM-dd"
                                  :enableTimePicker="false"
                                  :clearable="false"
                                  locale="es"
                                  autoApply
                                  @update:model-value="changeFilter"></datepicker>
                    </template>
                  </q-field>
                </div>
              </template>
              <template v-if="f.value === 'between_dates'">
                <div class="col-24 col-sm-8">
                  <q-field label="Fecha al" outlined dense stack-label class="x-datepicker">
                    <template v-slot:control>
                      <datepicker v-model="f.date_end"
                                  format="dd/MM/yyyy"
                                  modelType="yyyy-MM-dd"
                                  :enableTimePicker="false"
                                  :clearable="false"
                                  locale="es"
                                  autoApply
                                  @update:model-value="changeDateEnd"></datepicker>
                    </template>
                  </q-field>
                </div>
              </template>
              <template v-if="f.value === 'month' || f.value === 'between_months'">
                <div class="col-24 col-sm-8">
                  <q-field label="Mes del" outlined dense stack-label class="x-datepicker">
                    <template v-slot:control>
                      <datepicker v-model="f.month_start"
                                  monthPicker
                                  :clearable="false"
                                  locale="es"
                                  autoApply
                                  @update:model-value="changeFilter"></datepicker>
                    </template>
                  </q-field>
                </div>
              </template>
              <template v-if="f.value === 'between_months'">
                <div class="col-24 col-sm-8">
                  <q-field label="Mes al" outlined dense stack-label class="x-datepicker">
                    <template v-slot:control>
                      <datepicker v-model="f.month_end"
                                  monthPicker
                                  :clearable="false"
                                  locale="es"
                                  autoApply
                                  @update:model-value="changeFilter"></datepicker>
                    </template>
                  </q-field>
                </div>
              </template>
            </div>
          </div>
          <div :class="f.class" v-if="f.type === 'input'">
            <q-input :label="f.label"
                     outlined
                     dense
                     v-model="f.value"
                     debounce="750"
                     autocomplete="off"
                     aria-autocomplete="off"
                     @update:model-value="changeFilter">
              <template v-slot:append>
                <q-icon name="fa-light fa-search" size="xs"/>
              </template>
            </q-input>
          </div>
          <div :class="f.class" v-if="f.type === 'select_items'">
            <q-select :label="f.label"
                      outlined
                      dense
                      options-dense
                      v-model="f.value"
                      clearable
                      use-input
                      hide-selected
                      fill-input
                      input-debounce="750"
                      :options="items"
                      option-value="id"
                      option-label="name"
                      emit-value
                      map-options
                      @filter="filterFn"
                      @update:model-value="changeFilter"
                      style="width: 100%">
              <template v-slot:no-option>
                <q-item>
                  <q-item-section class="text-grey">
                    Sin resultados
                  </q-item-section>
                </q-item>
              </template>
            </q-select>
          </div>
        </template>
      </div>
    </q-card-section>
    <q-card-section v-if="widgets && widgets.length > 0" class="x-table-section-widgets">
      <div class="row q-col-gutter-sm">
        <div class="col-24 col-sm text-center column" v-for="w in widgets">
          <div class="x-table-widget">
            <div class="x-table-widget-label">{{ w.label }}</div>
            <div class="x-table-widget-value">{{ w.value }}</div>
          </div>
        </div>
      </div>
    </q-card-section>
    <q-card-section class="x-table-section-content">
      <q-table ref="tableRef"
               :rows="records"
               :columns="columns"
               :loading="loading"
               v-model:pagination="pagination"
               :visible-columns="visibleColumns"
               flat
               row-key="id"
               @request="onRequest"
               binary-state-sort
               table-class="table-index">
        <template v-slot:body="props">
          <slot name="table-rows" :props="props"></slot>
        </template>
      </q-table>
    </q-card-section>
    <x-loading :loading="loading"></x-loading>
  </q-card>
</template>

<script>

import Datepicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css'
import {api} from "boot/axios";
import {onMounted, ref} from "vue";
import _ from "lodash";
import {date} from "quasar";
import XLoading from "components/XLoading.vue";

export default {
  name: 'XTableServerNew',
  components: {XLoading, Datepicker},
  props: {
    title: {
      type: String,
      required: true,
    },
    resourceInit: {
      default: null,
    },
    resource: {
      type: String,
      required: true,
    },
    showSectionTitle: {
      type: Boolean,
      default: true
    },
    showFilter: {
      type: Boolean,
      default: false
    },
    showButtonNew: {
      type: Boolean,
      default: true
    },
    showButtonExport: {
      type: Boolean,
      default: false
    },
    showButtonColumns: {
      type: Boolean,
      default: true
    },
    additionalFilters: {
      type: Array,
      default: []
    }
  },
  setup(props, {emit}) {
    const tableRef = ref();
    let loading = ref(false);
    let loadingExport = ref(false);
    let tableName = ref(null);
    let columns = ref([]);
    let filters = ref([]);
    let widgets = ref([]);
    let records = ref([]);
    let pagination = ref({
      sortBy: null,
      descending: null,
      page: 1,
      rowsPerPage: 10,
      rowsNumber: 10,
      pageSizes: []
    });
    let visibleAllColumns = ref([]);
    let visibleColumns = ref([]);
    let selectVisibleColumns = ref([]);
    let selectColumns = ref([]);
    let items = ref([]);

    const initTableServer = () => {
      pagination.value = {
        sortBy: null,
        descending: null,
        page: 1,
        rowsPerPage: 1,
        rowsNumber: 1,
        pageSizes: []
      };
    }

    const getVisibleColumns = () => {
      let svc = [];
      columns.value.forEach(col => {
        if (!col.locked) {
          selectColumns.value.push({
            'label': col.label,
            'value': col.name,
          });
          if (visibleAllColumns.value.includes(col.name)) {
            svc.push(col.name);
          }
        }
      })
      selectVisibleColumns.value = svc;
    }

    const changeSelectVisibleColumns = (update = true) => {
      let vc = selectVisibleColumns.value
      let h = [];
      columns.value.forEach(hc => {
        if (vc.indexOf(hc.name) > -1 || hc.locked) {
          h.push(hc.name)
        }
      });
      visibleColumns.value = h;
      if (update) {
        updateVisibleColumns();
      }
    }

    const updateVisibleColumns = async () => {
      await api.post(`/${props.resource}/update_visible_columns`, {
        'tableName': tableName.value,
        'visible_columns': selectVisibleColumns.value
      })
    }

    const getInitTable = async () => {
      loading.value = true;
      let resourceInit = (!props.resourceInit) ? props.resource : props.resourceInit;
      await api.get(`/${resourceInit}/init_data_table`)
        .then(response => {
          tableName.value = response.data.table_name;
          columns.value = response.data.columns;
          filters.value = response.data.filters;
          visibleAllColumns.value = response.data.visible_columns;
          pagination.value.sortBy = response.data.pagination.sort_by;
          pagination.value.descending = response.data.pagination.descending;
          pagination.value.rowsPerPage = response.data.pagination.limit;
          pagination.value.pageSizes = _.orderBy(response.data.pagination.page_sizes);
        });
      getVisibleColumns();
      changeSelectVisibleColumns(false)
      loading.value = false;
    }

    const onRequest = async (data) => {
      const {page, rowsPerPage, sortBy, descending} = data.pagination
      const fetchCount = rowsPerPage === 0 ? pagination.value.rowsNumber : rowsPerPage;
      loading.value = true;
      records.value = [];
      widgets.value = [];

      await api.post(`/${props.resource}/records`, {
        'tableName': tableName.value,
        'page': page,
        'limit': fetchCount,
        'sortBy': sortBy,
        'descending': descending,
        'filters': filters.value.concat(props.additionalFilters),
      })
        .then(response => {
          let data = response.data;
          records.value = data.data;
          widgets.value = data.widgets;
          pagination.value.page = data.meta.current_page
          pagination.value.rowsPerPage = data.meta.per_page
          pagination.value.rowsNumber = data.meta.total
          pagination.value.sortBy = data.meta.sort_by
          pagination.value.descending = data.meta.descending
        });
      loading.value = false;
    }

    const changeFilter = async () => {
      await onRequest({pagination: pagination.value});
    }

    const changePageSize = async () => {
      pagination.value.page = 1;
      await changeFilter();
    }

    const clickExport = async (type, additionalFilters = null) => {
      loading.value = true;
      loadingExport.value = true;
      const {sortBy, descending} = pagination.value
      let aFilters = additionalFilters ? additionalFilters : props.additionalFilters

      await api({
        url: `/${props.resource}/export`,
        method: 'POST',
        responseType: 'blob',
        data: {
          'sortBy': sortBy,
          'descending': descending,
          'filters': filters.value.concat(aFilters),
          'select_columns': selectVisibleColumns.value,
          'type': type,
        }
      })
        .then(response => {
          const url = window.URL.createObjectURL(new Blob([response.data]));
          const link = document.createElement('a');
          link.href = url;
          let fileName = 'Reporte_' + props.title.replace(/\s+/g, '_') + '_' + date.formatDate(Date.now(), 'YYYYMMDDHHmmss')
          let extension = (type === 'xls') ? '.xlsx' : '.pdf';
          link.setAttribute('download', fileName + extension);
          document.body.appendChild(link);
          link.click();
        })
        .catch(error => {
          console.log(error)
        });
      loadingExport.value = false;
      loading.value = false;
    }

    const changeDateStart = () => {
      let period = _.find(filters.value, {'field': 'period'});
      if (period.date_start > period.date_end) {
        period.date_end = period.date_start;
      }
      changeFilter();
    }

    const changeDateEnd = () => {
      let period = _.find(filters.value, {'field': 'period'});
      if (period.date_end < period.date_start) {
        period.date_end = period.date_start;
      }
      changeFilter();
    }

    const changeMonthEnd = () => {
      let period = _.find(filters.value, {'field': 'period'});
      if (period.month_end < period.month_start) {
        period.month_end = period.month_start;
      }
      changeFilter();
    }

    const getFiltersInTable = () => {
      return _.filter(filters.value, {'in_table': true})
    }

    const changeFilterItem = () => {
      changeFilter();
    }

    const filterFn = (val, update, abort) => {
      update(() => {
          if (val !== '') {
            api.post(`/store/item_options`, {
              'search': val,
              'category_id': 'all',
            })
              .then(res => {
                items.value = res.data
              })
          }
        },
      )
    }

    const clickCreate = (value) => {
      emit('click-create', value)
    }

    onMounted(async () => {
      initTableServer();
      await getInitTable();
      tableRef.value.requestServerInteraction();
    })

    return {
      loading,
      loadingExport,
      tableRef,
      columns,
      pagination,
      filters,
      records,
      widgets,
      visibleColumns,
      selectColumns,
      selectVisibleColumns,
      items,
      changeSelectVisibleColumns,
      changeDateEnd,
      changeFilter,
      clickCreate,
      clickExport,
      filterFn,
      onRequest
    }
  },
}
</script>
