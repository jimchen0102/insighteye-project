<template>
  <q-dialog>
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
          flat
          label="取消"
          v-close-popup
        />
        <q-btn
          flat
          color="primary"
          label="確定"
          v-close-popup
          @click="handleDelete"
        />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script>
export default {
  props: {
    selected: {
      type: Array,
      default: () => [],
    },
  },

  setup(prosp, { emit }) {
    function handleDelete() {
      emit('delete');
    }

    return {
      handleDelete,
    }
  }
};
</script>
