<script setup>
  import {onMounted, ref, computed} from "vue";
  import RouterLink from "@/components/UI/RouterLink.vue";
  import Heading from "@/components/UI/Heading.vue";
  import Cliente from "@/components/Cliente.vue";
  import ClientService from "@/services/ClientService.js";

  const clientes = ref([]);

  onMounted(() => {
    ClientService.obtenerClientes()
        .then(({data}) => {
           console.log(data);
           clientes.value = data;
        })
        .catch(error => console.log('Hubo un error'));
  });

  defineProps({
    titulo: {
      type: String,
      required: true
    }
  });

  const existenClientes = computed(() => {
    return clientes.value.length > 0;
  });

  const actualizarEstado = (data) => {
    ClientService.cambiarEstado(data.id, {estado: !data.estado})
        .then(() => {
          const i = clientes.value.findIndex(cliente => cliente.id === data.id);
          clientes.value[i].estado = !data.estado;
        })
        .catch(error => console.log(error));
  }

  const eliminarCliente = id => {
    ClientService.eliminarCliente(id)
        .then(() => {
          clientes.value = clientes.value.filter(cliente => cliente.id !== id);
        })
        .catch(error => console.log(error))
  }

</script>

<template>
  <div>
    <div class="flex justify-end">
      <RouterLink to="agregar-cliente">
        Agregar Cliente
      </RouterLink>
    </div>
    <Heading>{{ titulo }}</Heading>
  </div>


  <div v-if="existenClientes" class="flow-root mx-auto  mt-10 p-5 bg-white shadow">
    <div class="-my-2 -mx-4 overflow-x-auto sm:-mx-6 lg:-mx-8">
      <div class="min-w-full py-2 align-middle sm:px-6 lg:px-8">
        <table class="min-w-full divide-y divide-gray-300">
          <thead>
            <tr>
              <th scope="col" class="p-2 text-left text-sm font-extrabold text-gray-600">Nombre</th>
              <th scope="col" class="p-2 text-left text-sm font-extrabold text-gray-600">Empresa</th>
              <th scope="col" class="p-2 text-left text-sm font-extrabold text-gray-600">Estado</th>
              <th scope="col" class="p-2 text-left text-sm font-extrabold text-gray-600">Acciones</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 bg-white">
            <Cliente
              v-for="cliente in clientes"
              :key="cliente.id"
              :cliente="cliente"
              @actualizar-estado="actualizarEstado"
              @eliminar-cliente="eliminarCliente"
            >
            </Cliente>
          </tbody>
        </table>
      </div>
    </div>
  </div>
  <p v-else class="text-center mt-10"> No hay clientes</p>
</template>