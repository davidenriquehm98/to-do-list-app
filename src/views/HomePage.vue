<template>
  <ion-page>
    <ion-tabs>
      <ion-tab tab="home">
        <div id="home-page" style="min-height: 75vh">
          <ion-header :translucent="true">
            <ion-toolbar>
              <ion-title>Inicio</ion-title>
            </ion-toolbar>
          </ion-header>
          <ion-content class="containerPage">
            <div class="titulo">
              <span> Inicio </span>
            </div>
            <div class="subtitulo">
              <span> David Enrique Hernández Macajola </span>
            </div>
            <div class="subtitulo">
              <span> 23001604 </span>
            </div>
            <ion-list style="min-height: 75vh">
              <ion-item>
                <ion-icon
                  aria-hidden="true"
                  :icon="warning"
                  color="warning"
                ></ion-icon>
                <ion-label class="ion-text-wrap">
                  <h2>
                    Tareas Pendientes
                  </h2>
                  <h3>{{ getTareasPendientes() }}</h3>
                </ion-label>
              </ion-item>
              <ion-item>
                <ion-icon
                  aria-hidden="true"
                  :icon="checkmarkCircle"
                  color="success"
                ></ion-icon>
                <ion-label class="ion-text-wrap">
                  <h2>
                    Tareas Completadas
                  </h2>
                  <h3>{{ getTareasCompletas() }}</h3>
                </ion-label>
              </ion-item>
            </ion-list>
          </ion-content>
        </div>
      </ion-tab>
      <ion-tab tab="addTask">
        <div id="addTask-page" style="min-height: 75vh">
          <ion-header>
            <ion-toolbar>
              <ion-title>Agregar Tarea</ion-title>
            </ion-toolbar>
          </ion-header>
          <ion-content>
            <ion-list style="min-height: 75vh">
              <ion-item>
                <ion-input
                  label="Titulo"
                  label-placement="stacked"
                  fill="outline"
                  placeholder="Titulo de Tarea"
                  v-model="newMessage.title"
                ></ion-input>
              </ion-item>
              <ion-item>
                <ion-radio-group
                  :value="newMessage.category"
                  @ionChange="changeCategory($event)"
                >
                  <ion-radio value="Trabajo">Trabajo</ion-radio>
                  <ion-radio value="Casa">Casa</ion-radio>
                  <ion-radio value="Negocio">Negocio</ion-radio>
                </ion-radio-group>
              </ion-item>
              <ion-item>
                <ion-textarea
                  label="Descripción"
                  label-placement="stacked"
                  placeholder="Descripción de la tarea"
                  :value="newMessage.description"
                  @input="changeDescr($event)"
                ></ion-textarea>
              </ion-item>
              <ion-item>
                <ion-buttons slot="end">
                  <ion-button color="success" @click="agregarTarea">
                    Agregar Tarea
                    <ion-icon slot="end" :icon="add"></ion-icon>
                  </ion-button>
                </ion-buttons>
              </ion-item>
            </ion-list>
          </ion-content>
        </div>
      </ion-tab>
      <ion-tab tab="library">
        <div id="library-page">
          <ion-header>
            <ion-toolbar>
              <ion-title>Todas las Tareas</ion-title>
            </ion-toolbar>
          </ion-header>
          <ion-content :fullscreen="true">
            <ion-refresher slot="fixed" @ionRefresh="refresh($event)">
              <ion-refresher-content></ion-refresher-content>
            </ion-refresher>
            <ion-list>
              <MessageListItem
                v-for="message in messages"
                :key="message.id"
                :message="message"
              />
            </ion-list>
          </ion-content>
        </div>
      </ion-tab>
      <ion-tab-bar slot="bottom">
        <ion-tab-button tab="home">
          <ion-icon :icon="home" />
          Inicio
        </ion-tab-button>
        <ion-tab-button tab="addTask">
          <ion-icon :icon="addCircle" />
          Agregar Tarea
        </ion-tab-button>
        <ion-tab-button tab="library">
          <ion-icon :icon="library" />
          Tareas
        </ion-tab-button>
      </ion-tab-bar>
    </ion-tabs>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonTabs,
  IonTab,
  IonToolbar,
  IonTabBar,
  IonTabButton,
  IonContent,
  IonHeader,
  IonTitle,
  IonIcon,
  IonList,
  IonPage,
  IonRefresher,
  IonRefresherContent,
  IonInput,
} from "@ionic/vue";
import { home, addCircle, library, add, warning, checkmarkCircle } from "ionicons/icons";
import MessageListItem from "@/components/MessageListItem.vue";
import { getMessages, addTask } from "@/data/messages";
import { onUpdated, ref, defineComponent } from "vue";
defineComponent({
  components: { IonInput },
});

const messages = ref(getMessages());
const newMessage = ref({ category: "Trabajo" });

const refresh = (ev: CustomEvent) => {
  setTimeout(() => {
    ev.detail.complete();
  }, 3000);
};

const agregarTarea = () => {
  const resultMessage = {
    ...newMessage.value,
    date: getDate() + " " + getTime(),
    isCompleted: false,
    id: messages.value.length + 1,
  };
  addTask(resultMessage);
  newMessage.value = { category: "Trabajo", description: "", title: null };
  updateListado();
};

const changeCategory = (ev) => {
  newMessage.value = { ...newMessage.value, category: ev.detail.value };
};

const changeDescr = (ev) => {
  newMessage.value = { ...newMessage.value, description: ev.target.value };
};

onUpdated(() => {
  updateListado();
});

const updateListado = () => {
  const _messages = getMessages();
  messages.value = [..._messages];
};

const getTareasPendientes = () => {
  const tareasPen = messages.value.filter((msg) => (!msg.isComplete))
  return tareasPen.length
}

const getTareasCompletas = () => {
  const tareasCom = messages.value.filter((msg) => (msg.isComplete))
  return tareasCom.length
}

function getDate() {
  let today = new Date();
  let dd = today.getDate();
  let mm = today.getMonth() + 1;
  let yyyy = today.getFullYear();
  if (dd < 10) {
    dd = "0" + dd;
  }
  if (mm < 10) {
    mm = "0" + mm;
  }
  today = dd + "/" + mm + "/" + yyyy;
  return today;
}

function getTime() {
  let today = new Date();
  let horas = today.getHours();
  let minutos = today.getMinutes();
  let lahora = "";
  horas = horas <= 9 ? "0" + horas : horas;
  minutos = minutos <= 9 ? "0" + minutos : minutos;
  lahora = horas + ":" + minutos;
  return lahora;
}
</script>

<style scoped>

ion-item ion-icon {
  font-size: 42px;
  margin-right: 8px;
}


ion-radio::part(container) {
  width: 30px;
  height: 30px;

  border-radius: 8px;
  border: 2px solid #ddd;
}

ion-radio::part(mark) {
  background: none;
  transition: none;
  transform: none;
  border-radius: 0;
}

ion-radio.radio-checked::part(container) {
  background: #6815ec;
  border-color: transparent;
}

ion-radio.radio-checked::part(mark) {
  width: 6px;
  height: 10px;

  border-width: 0px 2px 2px 0px;
  border-style: solid;
  border-color: #fff;

  transform: rotate(45deg);
}

.containerPage {
  display: flex;
  width: 100%;
  height: 85%;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  align-content: center;
}

.containerPage .titulo {
  width: 100%;
  font-weight: 700;
  font-size: 36px;
  padding: 2rem;
  text-align: center;
}

.containerPage .subtitulo {
  width: 100%;
  font-weight: 500;
  font-size: 18px;
  padding: 0.5rem;
  text-align: center;
}
</style>
