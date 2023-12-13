<template>
  <q-table
    class="q-mt-sm"
    row-key="name"
    :columns="columns"
    :rows="rows"
    separator="cell"
    flat
    bordered
    selection="multiple"
    :selected="selected"
    @update:selected="(row) => $emit('update:selected', row)"
  >
    <template v-slot:body="props">
      <q-tr :props="props">
        <q-td>
          <q-checkbox v-model="props.selected" />
        </q-td>
        <q-td key="name" :props="props">
          {{ props.row.name }}
          <q-tooltip>
            {{ props.row.name }}
          </q-tooltip>
        </q-td>
        <q-td key="cellphone" :props="props">
          {{ props.row.cellphone }}
          <q-tooltip>
            {{ props.row.cellphone }}
          </q-tooltip>
        </q-td>
        <q-td key="email" :props="props">
          {{ props.row.email }}
          <q-tooltip>
            {{ props.row.email }}
          </q-tooltip>
        </q-td>
        <q-td key="gender" :props="props">
          {{ props.row.gender }}
          <q-tooltip>
            {{ props.row.gender }}
          </q-tooltip>
        </q-td>
        <q-td key="birthday" :props="props">
          {{ props.row.birthday }}
          <q-tooltip>
            {{ props.row.birthday }}
          </q-tooltip>
        </q-td>
      </q-tr>
    </template>
  </q-table>
</template>

<script>
export default {
  props: {
    columns: {
      type: Array,
      default: () => [],
    },

    rows: {
      type: Array,
      default: () => [],
    },

    selected: {
      type: Array,
      default: () => [],
    },
  },
};
</script>
