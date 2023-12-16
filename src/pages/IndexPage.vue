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
      
      <QForm @submit="handleSearchEmployees" />

      <QTable
        :columns="state.columns"
        :rows="state.rows"
        v-model:selected="selected"
      />

      <QDialog
        :selected="selected"
        v-model="isModalOpen"
        @delete="handleDeleteEmployee"
      />
    </div>
  </q-page>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';
import { api } from 'src/boot/axios';
import QForm from 'src/components/QForm.vue';
import QTable from 'src/components/QTable.vue';
import QDialog from 'src/components/QDialog.vue';

export default {
  name: 'IndexPage',

  components: {
    QForm,
    QTable,
    QDialog,
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
        birthday: formateDate(employee.birthday),
      }));
    }

    function handleAddEmployee() {
      state.rows.unshift({
        name: Date.now(),
        cellphone: '',
        email: '',
        gender: '',
        birthday: '',
      });
    }

    function handleDeleteEmployee() {
      const selectedNames = selected.value.map((employee) => employee.name);
      const filteredEmployees = state.rows.filter((employee) => !selectedNames.includes(employee.name));
      state.rows = filteredEmployees;
      selected.value = [];
    }

    async function fetchFilterEmployees(search) {
      const res = await api.post(`${BASE_URL}/members/search`, {
        filter: {
          name: search.name,
          cellphone: search.cellphone,
          email: search.email,
          gender: search.gender,
        },
      });
      return res.data.members;
    }

    async function handleSearchEmployees(search) {
      try {
        const employees = await fetchFilterEmployees(search);
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
      isModalOpen,
      handleOpenModal,
      handleAddEmployee,
      handleDeleteEmployee,
      handleSearchEmployees,
    };
  },
};
</script>

<style lang="scss" scoped>
.block-item {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
}
</style>
