<template>
  <div
    class="w-80 px-4 py-6 bg-[#161438] text-white border border-white rounded-3xl"
  >
    <section class="flex justify-between mb-8">
      <div class="flex-col">
        <p class="text-xl font-medium">Volume</p>
        <button
          class="block bg-blue-800 p-2 w-16 h-12 rounded-md mt-1"
          @click="volumeUp"
        >
          Up
        </button>
        <button
          class="block bg-blue-800 p-2 w-16 h-12 rounded-md mt-2"
          @click="volumeDown"
        >
          Down
        </button>
      </div>
      <button class="" @click="enter">Enter</button>
      <div class="flex-col">
        <p class="text-xl font-medium">Apps</p>
        <button
          class="block bg-green-800 p-2 h-12 rounded mt-1 active:bg-green-900 active:outline outline-1 w-full"
        >
          Spotify
        </button>
        <button
          class="block bg-red-700 p-2 h-12 w-full rounded mt-2 active:bg-red-900 active:outline outline-1"
          @click="openYoutube"
        >
          Youtube
        </button>
      </div>
    </section>
    <footer>
      <p class="text-xl font-medium">Saved</p>
      <div class="flex-col gap gap-1">
        <button
          class="block p-1 text-white bg-purple-600 rounded mt-2"
          @click="openLofi"
        >
          Lofi channel 1
        </button>
        <button
          class="block p-1 text-white bg-purple-600 rounded mt-2"
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
