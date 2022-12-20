<script setup>
import { ref, onMounted} from 'vue'; 
import * as Stomp from "stomp-websocket";

const notifs = ref([])
const client = ref(Stomp.over(new SockJS("http://localhost:8989/stomp")));

const onConnect = () => {
  console.log("Connected to websocket")
  client.value.subscribe("/topic/sherpa-admins", (res) => {
    if (res.body) {
      console.log(res.body)
      notifs.value.push(JSON.parse(res.body))
    }
  })
}

onMounted(() => {
  client.value.connect({}, onConnect);
})

</script>

<template>
  <h1>Real Time Notifications</h1>
  <div v-for="notif in notifs" :key="notif.timestamp">
    <div class="subject">
      <span class="title">Subject : </span>
      <span class="subject">{{ notif.subject }}</span>
    </div>
    <div class="Content">
      <span class="title">Content : </span>
      <div v-if="typeof notif.content == 'object'">
        <div v-for="ct in notif.content">
          {{ `${ct} : ${notif.content[ct]}` }}
        </div>
      </div>
      <span v-else> {{ notif.content }} </span>
    </div>
    <div class="Status">
      <span class="title">Status : </span>
      <span class="Status">{{ notif.status }}</span>
    </div>
    <div class="Timestamp">
      <span class="title">Timestamp : </span>
      <span class="Timestamp">{{ notif.timestamp }}</span>
    </div>
    
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
