<template>
  <q-page class="q-pa-xl">
    <div class="text-accent text-subtitle1">
      Resumo dos pedidos
    </div>
    <div class="row q-mt-xl">
      <q-card class="col q-pa-sm shadow-1">
        <q-card-section>
          <q-avatar>
            <q-img
              :src="iconOrders"
              class="custom-icon"
            />
          </q-avatar>
        </q-card-section>
        <q-card-section>
          <div class="text-subtitle1 text-accent">200 pedidos</div>
          <div class="text-subtitle1 text-bold">R$ 10</div>
        </q-card-section>
      </q-card>
      <q-card class="col q-mx-md q-pa-sm shadow-1">
        <q-card-section>
          <q-avatar>
            <q-img
              :src="iconSales"
              class="custom-icon"
            />
          </q-avatar>
        </q-card-section>
        <q-card-section>
          <div class="text-subtitle1 text-accent">156 Vendas</div>
          <div class="text-subtitle1 text-bold">R$ 5</div>
        </q-card-section>
      </q-card>
      <q-card class="col q-pa-sm shadow-1">
        <q-card-section>
          <q-avatar>
            <q-img
              :src="iconTicket"
              class="custom-icon"
            />
          </q-avatar>
        </q-card-section>
        <q-card-section>
          <div class="text-subtitle1 text-accent">Ticket médio</div>
          <div class="text-subtitle1 text-bold">R$ 15</div>
        </q-card-section>
      </q-card>
    </div>
    <div class="q-mt-xl">
      <q-table
        class="my-sticky-virtscroll-table"
        virtual-scroll
        separator="cell"
        flat
        bordered
        dense
        v-model:pagination="pagination"
        :rows-per-page-options="[0]"
        :virtual-scroll-sticky-size-start="48"
        row-key="index"
        :rows="rows"
        :columns="columns"
      >
        <template v-slot:header="props">
          <q-tr :props="props" class="bg-primary text-white">
            <q-th
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              align="left"
              class="text-center"
            >
              {{ col.label }}
            </q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              align="left"
              class="text-center"
            >
              {{ col.value }}
            </q-td>
          </q-tr>
        </template>

        <template v-slot:bottom>
          <div class="row justify-around full-width">
            <q-btn-group flat class="col-6 q-px-md">
              <q-btn
                flat
                dense
                round
                color="primary"
                size="md"
                icon="first_page"
                :disable="pagination.page === 1"
                @click="setPage(1)"
              />
              <q-btn
                flat
                round
                color="primary"
                icon="chevron_left"
                :disable="pagination.page === 1"
                @click="prevPage"
              />
              <div
                v-for="i in visiblePages"
                :key="i"
                class="q-mx-xs q-py-sm"
              >
                <q-btn
                  flat
                  round
                  :color="pagination.page === i ? 'primary' : 'accent'"
                  @click="setPage(i)"
                >
                  {{ i }}
                </q-btn>
              </div>

              <q-btn
                flat
                round
                color="primary"
                icon="chevron_right"
                :disable="pagination.page === totalPages"
                @click="nextPage"
              />
              <q-btn
                flat
                dense
                round
                color="primary"
                icon="last_page"
                :disable="pagination.page === totalPages"
                @click="setPage(totalPages)"
              />
            </q-btn-group>
            <div class="col-2 q-py-md text-accent text-subtitle2">
              {{ pagination.page }} de
              {{ Math.ceil(rows.length / pagination.rowsPerPage) }} páginas
            </div>
            <div class="row q-py-sm col-3">
              <div
                class="col-6 q-mt-sm text-accent"
              >
                Linhas por página
              </div>
              <q-select
                class="col-6"
                outlined
                dense
                options-dense
                v-model="pagination.rowsPerPage"
                :options="[10, 20, 30, 50]"
              />
            </div>
          </div>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import iconOrders from '../assets/icon_orders.png';
import iconSales from '../assets/icon_sales.png';
import iconTicket from '../assets/icon_ticket.png';

defineOptions({
  name: 'Dashboard'
});

const pagination = ref({
  page: 1,
  rowsPerPage: 10
});
const rows = ref([...Array(100).keys()]);
const totalPages = computed(
  () => Math.ceil(rows.value.length / pagination.value.rowsPerPage)
);

const visiblePages = computed(() => {
  let start = Math.max(1, pagination.value.page - 2);
  let end = Math.min(totalPages.value, pagination.value.page + 2);

  if (end - start < 4) {
    if (pagination.value.page < 3) {
      end = Math.min(5, totalPages.value);
    } else if (pagination.value.page > totalPages.value - 2) {
      start = Math.max(totalPages.value - 4, 1);
    }
  }

  return Array.from({ length: end - start + 1 }, (_, i) => start + i);
});

function setPage(page: number) {
  pagination.value.page = page;
}

function prevPage() {
  if (pagination.value.page > 1) {
    pagination.value.page--;
  }
}

function nextPage() {
  if (pagination.value.page < totalPages.value) {
    pagination.value.page++;
  }
}

const columns = [
  { name: 'orderId', required: true, label: 'ID do Pedido', align: 'left', field: 'orderId', sortable: true },
  { name: 'storeId', align: 'left', label: 'ID da Loja', field: 'storeId', sortable: true },
  { name: 'creation', align: 'left', label: 'Criação', field: 'creation', sortable: true },
  { name: 'ClientName', align: 'left', label: 'Nome do cliente', field: 'clientName', sortable: true },
  { name: 'clientDocument', align: 'left', label: 'CPF/CNPJ do cliente', field: 'CPF/CNPJ do cliente', sortable: true },
  { name: 'orderStatus', align: 'left', label: 'Status do pedido', field: 'Status do pedido', sortable: true },
  { name: 'paymentStatus', align: 'left', label: 'Status do pagamento', field: 'Status do pagamento', sortable: true },
  { name: 'paymentMethod', align: 'left', label: 'Método de pagamento', field: 'Método de pagamento', sortable: true },
  { name: 'total', align: 'left', label: 'Total', field: 'total', sortable: true }
];

</script>
