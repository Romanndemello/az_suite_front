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
          <div
            class="text-subtitle1 text-accent"
          >
            {{ orders.totalOrders }} pedidos
          </div>
          <div
            class="text-subtitle1 text-bold"
          >
            {{ orders.totalOrdersValue }}
          </div>
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
          <div
            class="text-subtitle1 text-accent"
          >
            {{ orders.successfulSales }} Vendas
          </div>
          <div
            class="text-subtitle1 text-bold"
          >
            {{ orders.successfulSalesValue }}
          </div>
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
          <div
            class="text-subtitle1 text-accent"
          >
            Ticket médio
          </div>
          <div
            class="text-subtitle1 text-bold"
          >
            {{ orders.ticketAverage }}
          </div>
        </q-card-section>
      </q-card>
    </div>
    <div class="q-mt-xl">
      <q-table
        class="my-sticky-virtscroll-table"
        style="border-radius: 8px;"
        virtual-scroll
        separator="cell"
        flat
        bordered
        dense
        v-model:pagination="pagination"
        :rows-per-page-options="[0]"
        :virtual-scroll-sticky-size-start="48"
        row-key="index"
        :rows="ordersList"
        :columns="columns"
        @request="onRequest"
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
            <q-btn-group flat class="col-6 items-center">
              <q-btn
                flat
                dense
                round
                color="primary"
                size="md"
                icon="keyboard_double_arrow_left"
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
              >
                <q-btn
                  :flat="pagination.page !== i"
                  :unelevated="pagination.page === i"
                  round
                  :color="pagination.page === i ? 'primary' : 'accent'"
                  @click="setPage(i)"
                  :class="{ 'selected-btn': pagination.page === i }"
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
                icon="keyboard_double_arrow_right"
                :disable="pagination.page === totalPages"
                @click="setPage(totalPages)"
              />
            </q-btn-group>
            <div class="col-2 q-py-md text-accent text-subtitle2">
              {{ pagination.page }} de
              {{ Math.ceil(rows / pagination.rowsPerPage) }} páginas
            </div>
            <div class="row col-3 items-center">
              <div
                class="col-7 text-accent text-subtitle2"
              >
                Linhas por página
              </div>
              <q-select
                class="col-5"
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
import { computed, onBeforeMount, ref } from 'vue';
import { useRouter } from 'vue-router';
import iconOrders from '../assets/icon_orders.png';
import iconSales from '../assets/icon_sales.png';
import iconTicket from '../assets/icon_ticket.png';

const router = useRouter();
const orders = ref({
  totalOrders: 0,
  totalOrdersValue: 0,
  ticketAverage: 0,
  successfulSales: 0,
  successfulSalesValue: 0
});
const ordersList = ref([]);
onBeforeMount(() => {
  if(!localStorage.getItem('token')) {
    router.push('/login');
  }
  getOrders();
});

defineOptions({
  name: 'Dashboard'
});

const pagination = ref({
  page: 1,
  rowsPerPage: 10
});
const rows = ref([...Array(100).keys()]);

const totalPages = computed(() =>
  Math.ceil(rows.value / pagination.value.rowsPerPage)
);

const onRequest = async (props: any) => {
  console.log(props);
  const { page, rowsPerPage } = props.pagination;
  pagination.value.page = page;
  pagination.value.rowsPerPage = rowsPerPage;
  getOrders();
};

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
const getOrders = () => {
  const token = localStorage.getItem('token');
  const opt = {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${token}`
    }
  }
  fetch('http://localhost:3333/proof/dashboard', opt)
    .then((response) => response.json())
    .then((data) => {
      console.log(data);
      orders.value = data.ordersCards;
      ordersList.value = data.orderList;
      rows.value = data.totalOrders;
    })
    .catch((err) => {
      console.error(err);
    });
};
const columns = [
  {
    name: 'orderId', required: true, label: 'ID do Pedido',
    align: 'left', field: 'orderId', sortable: false
  },
  {
    name: 'storeId', align: 'left', label: 'ID da Loja',
    field: 'storeId', sortable: false
  },
  {
    name: 'creation', align: 'left', label: 'Criação',
    field: 'creation', sortable: false
  },
  {
    name: 'customerName', align: 'left', label: 'Nome do cliente',
    field: 'customerName', sortable: false
  },
  {
    name: 'customerDoc', align: 'left', label: 'CPF/CNPJ do cliente',
    field: 'customerDoc', sortable: false
  },
  {
    name: 'orderStatus', align: 'left', label: 'Status do pedido',
    field: 'orderStatus', sortable: false
  },
  {
    name: 'paymentStatus', align: 'left', label: 'Status do pagamento',
    field: 'paymentStatus', sortable: false
  },
  {
    name: 'paymentMethod', align: 'left', label: 'Método de pagamento',
    field: 'paymentMethod', sortable: false
  },
  {
    name: 'totalAmount', align: 'left', label: 'Total',
    field: 'totalAmount', sortable: false
  }
];

</script>


<style>
.selected-btn {
  background-color:#FE7C6E;
  color: white;
}
</style>
