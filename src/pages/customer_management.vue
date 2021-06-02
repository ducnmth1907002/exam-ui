<template>
  <q-page class="q-pa-sm">
    <q-card>
      <q-table
        title="Employee Management"
        :data="data"
        :hide-header="mode === 'grid'"
        :columns="columns"
        row-key="name"
        :grid="mode=='grid'"
        :filter="filter"
        :pagination.sync="pagination"
      >
        <template v-slot:top-right="props">
          <q-btn @click="new_customer=true" outline color="primary" label="Add New" class="q-mr-xs"/>

          <q-input outlined dense debounce="300" v-model="filter" placeholder="Search">
            <template v-slot:append>
              <q-icon name="search"/>
            </template>
          </q-input>

          <q-btn
            flat
            round
            dense
            :icon="props.inFullscreen ? 'fullscreen_exit' : 'fullscreen'"
            @click="props.toggleFullscreen"
            v-if="mode === 'list'"
          >
            <q-tooltip
              :disable="$q.platform.is.mobile"
              v-close-popup
            >{{ props.inFullscreen ? 'Exit Fullscreen' : 'Toggle Fullscreen' }}
            </q-tooltip>
          </q-btn>

          <q-btn
            flat
            round
            dense
            :icon="mode === 'grid' ? 'list' : 'grid_on'"
            @click="mode = mode === 'grid' ? 'list' : 'grid'; separator = mode === 'grid' ? 'none' : 'horizontal'"
            v-if="!props.inFullscreen"
          >
            <q-tooltip
              :disable="$q.platform.is.mobile"
              v-close-popup
            >{{ mode === 'grid' ? 'List' : 'Grid' }}
            </q-tooltip>
          </q-btn>
        </template>
      </q-table>
    </q-card>
    <q-dialog v-model="new_customer">
      <q-card style="width: 600px; max-width: 60vw;">
        <q-card-section>
          <div class="text-h6">
            Add new employee
            <q-btn round flat dense icon="close" class="float-right" color="grey-8" v-close-popup></q-btn>
          </div>
        </q-card-section>
        <q-separator inset></q-separator>
        <q-card-section class="q-pt-none">
          <q-form class="q-gutter-md">
            <q-list>
              <q-item>
                <q-item-section>
                  <q-item-label class="q-pb-xs">Name</q-item-label>
                  <q-input dense outlined v-model="customer.name" label="Name"/>
                </q-item-section>
              </q-item>
              <q-item>
                <q-item-section>
                  <q-item-label class="q-pb-xs">Wage</q-item-label>
                  <q-input type="number" dense outlined v-model="customer.wage" label="Wage"/>
                </q-item-section>
              </q-item>
            </q-list>
          </q-form>
        </q-card-section>

        <q-card-actions align="right" class="text-teal">
          <q-btn label="Save" type="submit" color="primary" @click="createEmployee" v-close-popup/>
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      filter: "",
      customer: {},
      new_customer: false,
      mode: "list",
      columns: [
        {
          name: "id",
          required: true,
          label: "Id",
          align: "left",
          field: "id",
          sortable: true
        },
        {
          name: "name",
          align: "left",
          label: "Name",
          field: "name",
          sortable: true
        },
        {
          name: "wage",
          align: "left",
          label: "Wage",
          field: "wage",
          sortable: true
        },
      ],
      data: [],
      pagination: {
        rowsPerPage: 10
      }
    };
  },
  methods: {
    getEmployee() {
      axios.get('http://localhost:8000/employees').then(res => {
        this.data = res.data
      })
    },
    createEmployee() {
      axios.post('http://localhost:8000/employees', {
        name: this.customer.name,
        wage: this.customer.wage
      }).then(() => {
        this.getEmployee()
      })
    }
  },
  created() {
    this.getEmployee()
  }
};
</script>
