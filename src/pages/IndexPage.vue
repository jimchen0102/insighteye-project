<template>
  <q-page class="row justify-left q-pa-lg">
    <div class="block-item">
      <div class="row justify-between items-center">
        <div class="text-h6">員工基本資訊</div>
        <div>
          <q-btn
            class="q-mr-sm"
            color="primary"
            label="新增"
            @click="handleAddEmployee"
          />
          <q-btn
            color="white"
            text-color="black"
            label="刪除"
            @click="handleOpenModal"
          />
        </div>
      </div>

      <div class="row items-center q-mt-sm">
        <div class="col-11">
          <div class="row">
            <div class="col-3">
              <q-input
                v-model="search.name"
                label="姓名"
              />
            </div>
            <div class="col-3">
              <q-input
                v-model="search.cellphone"
                label="手機"
              />
            </div>
            <div class="col-3">
              <q-input
                v-model="search.email"
                label="信箱"
              />
            </div>
            <div class="col-3">
              <q-select
                v-model="search.gender"
                :options="gender"
                label="性別"
              />
            </div>
          </div>
        </div>
        <div class="col-1">
          <q-btn
            flat
            color="primary"
            icon="search"
            @click="handleSearchEmployees"
          />
        </div>
      </div>

      <QTable
        :columns="state.columns"
        :rows="state.rows"
        v-model:selected="selected"
      />

      <q-dialog v-model="isModalOpen">
        <q-card style="width: 360px">
          <q-card-section>
            <div class="text-h6 text-center">刪除</div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            <p class="text-center">
              <template v-if="selected.length > 0">
                是否確定刪除 {{ selected.length }} 筆資料
              </template>
              <template v-else>
                尚未選取刪除的資料
              </template>
            </p>
          </q-card-section>

          <q-card-actions align="center">
            <q-btn
              color="white"
              text-color="black"
              label="取消"
              v-close-popup
            />
            <q-btn
              color="primary"
              label="確定"
              v-close-popup
              @click="handleDeleteEmployee"
            />
          </q-card-actions>
        </q-card>
      </q-dialog>
    </div>
  </q-page>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';
import { api } from '../boot/axios';
import QTable from 'src/components/QTable.vue';

export default {
  name: 'IndexPage',

  components: {
    QTable
  },

  setup () {
    const BASE_URL = 'http://35.194.177.50:7777';

    const state = reactive({
      columns: [
        {
          name: 'name',
          label: '姓名',
          align: 'left',
          field: 'name',
          sortable: true,
        },
        {
          name: 'cellphone',
          label: '手機',
          align: 'left',
          field: 'cellphone',
          sortable: true,
        },
        {
          name: 'email',
          label: '信箱',
          align: 'left',
          field: 'email',
          sortable: true,
        },
        {
          name: 'gender',
          label: '性別',
          align: 'left',
          field: 'gender',
          sortable: true,
        },
        {
          name: 'birthday',
          label: '生日',
          align: 'left',
          field: 'birthday',
          sortable: true,
        },
      ],
      rows: [],
    });
    const selected = ref([]);
    const search = reactive({
      name: '',
      cellphone: '',
      email: '',
      gender: ''
    });
    const gender = reactive(['男', '女']);
    const isModalOpen = ref(false);

    function handleOpenModal() {
      isModalOpen.value = true;
    }

    function formateDate(date) {
      const [yyyymmdd] = date.split('T');
      const [year, month, day] = yyyymmdd.split('-');
      const formateMonth = month.length === 1 ? `0${month}` : month;
      const formateDay = day.length === 1 ? `0${day}` : day;
      return `${year}/${formateMonth}/${formateDay}`;
    }

    function formateEmployees(employees) {
      return employees.map((employee) => ({
        ...employee,
        birthday: formateDate(employee.birthday)
      }));
    }

    function handleAddEmployee() {
      state.rows.unshift({
        name: '',
        cellphone: '',
        email: '',
        gender: '',
        birthday: ''
      });
    }

    function handleDeleteEmployee() {
      const selectedNames = selected.value.map(employee => employee.name);
      const filteredEmployees = state.rows.filter(employee => !selectedNames.includes(employee.name));
      state.rows = filteredEmployees;
      selected.value = [];
    }

    async function fetchFilterEmployees() {
      const res = await api.post(`${BASE_URL}/members/search`, {
        filter: {
          name: search.name,
          cellphone: search.cellphone,
          email: search.email,
          gender: search.gender
        }
      });
      return res.data.members;
    }

    async function handleSearchEmployees() {
      try {
        const employees = await fetchFilterEmployees();
        state.rows = formateEmployees(employees);
      } catch (error) {
        throw new Error(error);
      }
    }

    async function fetchEmployees() {
      const res = await api.get(`${BASE_URL}/members`);
      return res.data.members;
    }

    onMounted(async () => {
      try {
        const employees = await fetchEmployees();
        state.rows = formateEmployees(employees);
      } catch (error) {
        throw new Error(error);
      }
    });

    return {
      state,
      selected,
      search,
      gender,
      isModalOpen,
      handleOpenModal,
      handleAddEmployee,
      handleDeleteEmployee,
      handleSearchEmployees
    }
  }
};
</script>

<style lang="scss" scoped>
.block-item {
  min-width: 400px;
}
</style>
