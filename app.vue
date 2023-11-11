<template>
  <div>
    <v-app>
      <v-container>
        <v-card>
          <v-card-title>Real-Time Chat</v-card-title>
          <v-card-text>
            <v-list>
              <v-list-item v-for="message in messages" :key="message.id">
                <v-list-item-content>{{ message.text }}</v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card-text>
          <v-card-actions>
            <v-text-field label="入力してください。" v-model="newMessage" @keyup.enter="sendMessage" />
            <v-btn color="primary" @click="sendMessage">Send</v-btn>
          </v-card-actions>
        </v-card>
      </v-container>
    </v-app>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import { vApp, vContainer, vCard, vCardTitle, vCardText, vList, vListItem, vListItemContent, vCardActions, vTextField, vBtn } from 'vuetify/components';

const messages = ref([]);
const newMessage = ref('');
let ws;

onMounted(() => {
  ws = new WebSocket('ws://localhost:3000');

  ws.onmessage = (event) => {
    const message = JSON.parse(event.data);
    messages.value.push(message);
  };
});


onUnmounted(() => {
  ws.close();
});

const sendMessage = () => {
  if (newMessage.value.trim()) {
    ws.send(JSON.stringify({ text: newMessage.value }));
    newMessage.value = '';
  }
};
</script>
