<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-back-button
            :text="getBackButtonText()"
            default-href="/"
          ></ion-back-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true" v-if="message">
      <ion-item>
        <ion-icon
          aria-hidden="true"
          :icon="message.isComplete ? checkmarkCircle : warning"
          :color="message.isComplete ? 'success' : 'warning'"
        ></ion-icon>
        <ion-label class="ion-text-wrap">
          <h2>
            {{ message.title }}
          </h2>
          <span class="date">
            <ion-note>{{ message.date }}</ion-note>
          </span>
          <h3>{{ message.category }}</h3>
        </ion-label>
      </ion-item>

      <div class="ion-padding">
        <p>
          {{ message.description }}
        </p>
      </div>
      <ion-item >
        <ion-buttons v-if="!message.isComplete" slot="end">
          <ion-button color="success" @click="completeTask" >
            Completar
            <ion-icon slot="end" :icon="checkmark"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-item>
      <ion-item>
        <ion-buttons slot="end">
          <ion-button color="danger" @click="deleteTask" >
            Eliminar
            <ion-icon slot="end" :icon="trashBin"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-item>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { useRoute } from "vue-router";
import {
  IonBackButton,
  IonButtons,
  IonContent,
  IonHeader,
  IonIcon,
  IonItem,
  IonLabel,
  IonNote,
  IonPage,
  IonToolbar,
} from "@ionic/vue";
import { warning, checkmarkCircle, trashBin, checkmark } from "ionicons/icons";
import { getMessage, onDelete, onUpdate } from "../data/messages";
import { ref } from "vue";
import router from "@/router";

const getBackButtonText = () => {
  const win = window as any;
  const mode = win && win.Ionic && win.Ionic.mode;
  return mode === "ios" ? "Inbox" : "";
};

const route = useRoute();
const message = ref(getMessage(parseInt(route.params.id as string)));

const completeTask = () => {
  const newTask = { ...message.value, isComplete: true }
  onUpdate(newTask);
  message.value = newTask;
}

const deleteTask = () => {
  onDelete(message.value)
  router.push("/")
}
</script>

<style scoped>
ion-item {
  --inner-padding-end: 0;
  --background: transparent;
}

ion-label {
  margin-top: 12px;
  margin-bottom: 12px;
}

ion-item h2 {
  font-weight: 600;

  /**
   * With larger font scales
   * the date/time should wrap to the next
   * line. However, there should be
   * space between the name and the date/time
   * if they can appear on the same line.
   */
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

ion-item .date {
  align-items: center;
  display: flex;
}

ion-item ion-icon {
  font-size: 42px;
  margin-right: 8px;
}

ion-item ion-note {
  font-size: 0.9375rem;
  margin-right: 12px;
  font-weight: normal;
}

h1 {
  margin: 0;
  font-weight: bold;
  font-size: 1.4rem;
}

p {
  line-height: 1.4;
}
</style>
