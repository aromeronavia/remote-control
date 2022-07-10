<template>
  <div class="w-screen px-4 py-6">
    <section class="flex justify-between mb-8">
      <div class="flex-col">
        <p>Volume</p>
        <button class="block" @click="volumeUp">Up</button>
        <button class="block" @click="volumeDown">Down</button>
      </div>
      <button @click="enter">Enter</button>
      <div class="flex-col">
        <button class="block">Spotify</button>
        <button class="block" @click="openYoutube">Youtube</button>
      </div>
    </section>
    <footer>
      <p>Lofi</p>
      <div class="flex-wrap">
        <button @click="openLofi">Lofi channel 1</button>
        <button
          class="p-1 text-white bg-purple-600 rounded"
          @click="openTransistorOst"
        >
          Transistor OST
        </button>
      </div>
    </footer>
  </div>
</template>

<script lang="ts">
import Vue from "vue";

type Data = {
  socket: WebSocket | null;
};

export default Vue.extend({
  name: "IndexPage",
  data(): Data {
    return {
      socket: null,
    };
  },
  methods: {
    volumeUp() {
      const payload = {
        method: "ms.remote.control",
        params: {
          Cmd: "Click",
          DataOfCmd: "KEY_VOLUP",
          Option: "false",
          TypeOfRemote: "SendRemoteKey",
        },
      };
      this.socket?.send(JSON.stringify(payload));
    },
    volumeDown() {
      const payload = {
        method: "ms.remote.control",
        params: {
          Cmd: "Click",
          DataOfCmd: "KEY_VOLDOWN",
          Option: "false",
          TypeOfRemote: "SendRemoteKey",
        },
      };
      this.socket?.send(JSON.stringify(payload));
    },
    async openYoutube() {
      this.openYoutubeVideo("tA8yqkwNp_M");
    },
    async openYoutubeVideo(videoId?: string) {
      await fetch("http://192.168.3.12:8080/ws/apps/YouTube", {
        method: "post",
        body: `v=${videoId}`,
      });

      setTimeout(() => {
        this.enter();
      }, 5000);
    },
    enter() {
      const payload = {
        method: "ms.remote.control",
        params: {
          Cmd: "Click",
          DataOfCmd: "KEY_ENTER",
          Option: "false",
          TypeOfRemote: "SendRemoteKey",
        },
      };
      this.socket?.send(JSON.stringify(payload));
    },
    openLofi() {
      this.openYoutubeVideo("LtXwnvjdN_o");
    },
    openTransistorOst() {
      this.openYoutubeVideo("-zA1jRmAYfU");
    },
  },
  mounted: function () {
    this.socket = new WebSocket(
      "ws://192.168.3.12:8001/api/v2/channels/samsung.remote.control?name=Yolo"
    );
    this.socket.onmessage = function (message: MessageEvent<any>) {
      console.log(JSON.parse(message.data));
    };
  },
  beforeDestroy() {
    this.socket?.close();
  },
});
</script>
