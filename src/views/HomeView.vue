<!-- Second website (Vue.js) -->
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <button v-if="deferredPrompt" @click="installApp" id="bonjour">
      Install App
    </button>
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from "@/components/HelloWorld.vue";

export default {
  name: "HomeView",
  components: {
    HelloWorld,
  },
  data() {
    return {
      deferredPrompt: null,
    };
  },
  mounted() {
    window.addEventListener("beforeinstallprompt", (event) => {
      // Prevent Chrome 67 and earlier from automatically showing the prompt
      event.preventDefault();

      // Stash the event so it can be triggered later.
      this.deferredPrompt = event;

      // Update UI to notify the user that the app can be installed
    });

    // Event listener to receive messages
    window.addEventListener("message", this.receiveMessage, false);
  },
  methods: {
    receiveMessage(event) {
      // Check the origin of the sender
      if (event.origin === "https://peaceful-khapse-9704d6.netlify.app") {
        // Process the received data
        const { action } = event.data;

        // Call the installApp function if the action is 'installApp'
        if (action === "installApp") {
          this.installApp();
        }
      }
    },
    installApp() {
      // Show the install prompt
      if (this.deferredPrompt) {
        this.deferredPrompt.prompt();

        // Wait for the user to respond to the prompt
        this.deferredPrompt.userChoice.then((choiceResult) => {
          if (choiceResult.outcome === "accepted") {
            console.log("User accepted the install prompt");
          } else {
            console.log("User dismissed the install prompt");
          }

          // Reset deferredPrompt to null as it can only be used once
          this.deferredPrompt = null;
        });
      }
    },
  },
  beforeDestroy() {
    // Remove the event listener when the component is destroyed
    window.removeEventListener("message", this.receiveMessage);
  },
};
</script>

<style>
/* Your styles go here */
</style>
