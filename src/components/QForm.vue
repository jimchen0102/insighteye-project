<template>
  <q-form
    class="q-mt-sm"
    @reset="handleReset"
    @submit="handleSubmit"
  >
    <div class="row items-center q-gutter-md">
      <div class="col">
        <div class="row">
          <div class="col-6 col-sm">
            <q-input
              v-model="search.name"
              label="姓名"
            />
          </div>
          <div class="col-6 col-sm">
            <q-input
              v-model="search.cellphone"
              label="手機"
            />
          </div>
          <div class="col-6 col-sm">
            <q-input
              v-model="search.email"
              label="信箱"
            />
          </div>
          <div class="col-6 col-sm">
            <q-select
              v-model="search.gender"
              :options="gender"
              label="性別"
            />
          </div>
        </div>
      </div>
      <div class="col-auto">
        <q-btn
          type="submit"
          flat
          round
          color="primary"
          icon="search"
        />
        <q-btn
          type="reset"
          class="q-ml-sm"
          flat
          round
          color="grey"
          icon="delete"
        />
      </div>
    </div>
  </q-form>
</template>

<script>
import { ref, reactive } from 'vue';

export default {
  emits: ['submit'],

  setup (props, { emit }) {
    const search = ref({
      name: '',
      cellphone: '',
      email: '',
      gender: '',
    });
    const gender = reactive(['男', '女']);

    function handleSubmit() {
      emit('submit', search.value);
    }

    function handleReset() {
      search.value = {
        name: '',
        cellphone: '',
        email: '',
        gender: '',
      };
      handleSubmit();
    }

    return {
      search,
      gender,
      handleSubmit,
      handleReset,
    };
  },
};
</script>