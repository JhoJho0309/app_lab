<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Nuevo usuario</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <form @submit.prevent="guardar">
        <ion-list>
          <ion-item>
            <ion-input
              label="Nombre"
              label-placement="floating"
              v-model="form.name"
              required
            />
          </ion-item>

          <ion-item>
            <ion-input
              label="Usuario"
              label-placement="floating"
              v-model="form.username"
              required
            />
          </ion-item>

          <ion-item>
            <ion-input
              label="Correo"
              type="email"
              inputmode="email"
              label-placement="floating"
              v-model="form.email"
              required
            />
          </ion-item>

          <ion-item>
            <ion-input
              label="Teléfono"
              type="tel"
              inputmode="tel"
              label-placement="floating"
              v-model="form.phone"
            />
          </ion-item>

          <ion-item>
            <ion-input
              label="Sitio web"
              type="url"
              label-placement="floating"
              v-model="form.website"
            />
          </ion-item>
        </ion-list>

        <div class="ion-padding">
          <ion-button expand="block" type="submit" :disabled="enviando">
            {{ enviando ? 'Guardando…' : 'Guardar' }}
          </ion-button>
        </div>
      </form>

      <ion-text color="danger" v-if="error">
        <p class="ion-padding ion-no-margin">{{ error }}</p>
      </ion-text>

      <ion-card v-if="creado">
        <ion-card-header>
          <ion-card-title>Usuario creado</ion-card-title>
          <ion-card-subtitle>id asignado: {{ creado.id }}</ion-card-subtitle>
        </ion-card-header>
        <ion-card-content>
          <p>{{ creado.name }}</p>
          <p>{{ creado.email }}</p>
        </ion-card-content>
      </ion-card>

      <ion-toast
        :is-open="mostrarToast"
        message="Usuario creado"
        :duration="2000"
        @didDismiss="mostrarToast = false"
      />
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue';
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonList,
  IonItem,
  IonInput,
  IonButton,
  IonText,
  IonToast,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardSubtitle,
  IonCardContent,
} from '@ionic/vue';
import type { Usuarios } from '@/interfaces/usuarios';

const form = reactive({
  name: '',
  username: '',
  email: '',
  phone: '',
  website: '',
});

const enviando = ref(false);
const error = ref<string | null>(null);
const creado = ref<Usuarios | null>(null);
const mostrarToast = ref(false);

async function guardar() {
  enviando.value = true;
  error.value = null;
  creado.value = null;

  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form),
    });

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    creado.value = await response.json();
    mostrarToast.value = true;
    limpiar();
  } catch (e) {
    error.value = 'No se pudo crear el usuario. Revisá la conexión e intentá de nuevo.';
    console.error(e);
  } finally {
    enviando.value = false;
  }
}

function limpiar() {
  form.name = '';
  form.username = '';
  form.email = '';
  form.phone = '';
  form.website = '';
}
</script>