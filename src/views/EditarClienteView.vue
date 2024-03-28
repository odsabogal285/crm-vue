<script setup>
  import {onMounted, reactive} from "vue";
  import {FormKit} from '@formkit/vue'
  import {useRoute, useRouter} from 'vue-router';
  import RouterLink from "@/components/UI/RouterLink.vue";
  import Heading from "@/components/UI/Heading.vue";
  import ClientService from "@/services/ClientService.js";

  const router = useRouter();
  const route = useRoute();

  const {id} = route.params;

  const formData = reactive({});

  onMounted(() => {
    ClientService.obtenerCliente(id)
        .then(({data}) => {
          console.log(data);
          Object.assign(formData, data);
        })
        .catch(error => console.log(error))
  });

  defineProps({
    titulo: {
      type: String,
      required: true
    }
  });
  const handleSubmit = (data) => {
    ClientService.actualizarCliente(id, data)
        .then(() => router.push({name: 'listado-clientes'}))
        .catch(error => console.log(error))
  }

</script>

<template>
  <div>
    <div class="flex justify-end">
      <RouterLink to="listado-clientes">
        Volver
      </RouterLink>
    </div>

    <Heading>{{ titulo }}</Heading>

    <div class="mx-auto mt-10 bg-white shadow">
      <div class="mx-auto md:w-2/3 py-20 px-6">
        <FormKit
            type="form"
            :actions="false"
            incomplete-message="No se pudo enviar, revisa los mensajes"
            @submit="handleSubmit"
            :value="formData"
        >
          <FormKit
              type="text"
              label="Nombre"
              name="nombre"
              placeholder="Nombre del Cliente"
              validation="required"
              :validation-messages="{required: 'El Nombre del cliente es obligatorio'}"
              v-model="formData.nombre"
          />
          <FormKit
              type="text"
              label="Apellido"
              name="apellido"
              placeholder="Apellido del Cliente"
              validation="required"
              :validation-messages="{required: 'El Apellido del cliente es obligatorio'}"
              v-model="formData.apellido"
          />
          <FormKit
              type="email"
              label="Email"
              name="email"
              placeholder="Email del Cliente"
              validation="required|email"
              :validation-messages="{required: 'El Email del cliente es obligatorio', email: 'Coloca un email válido'}"
              v-model="formData.email"
          />
          <FormKit
              type="text"
              label="Teléfono"
              name="telefono"
              placeholder="Teléfono: XXX-XXX-XXXX"
              validation="*matches:/^[0-9]{3}-[0-9]{3}-[0-9]{4}$/"
              :validation-messages="{matches: 'El formato no es válido'}"
              v-model="formData.telefono"
          />
          <FormKit
              type="text"
              name="empresa"
              label="Empresa"
              placeholder="Empresa del Cliente"
              v-model="formData.empresa"
          />
          <FormKit
              type="text"
              name="puesto"
              label="Puesto"
              placeholder="Puesto del Cliente"
              v-model="formData.puesto"
          />
          <FormKit
              type="submit"
              label="Actualizar Cliente"
          />
        </FormKit>
      </div>
    </div>
  </div>
</template>
<style>
  .formkit-wrapper {
    max-width: 100%;
  }
</style>