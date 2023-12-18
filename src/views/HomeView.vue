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
    window.addEventListener("message", function (event) {
      // Check the origin of the message
      if (event.origin === "https://your-first-website-url.com") {
        // Check the message data
        if (event.data === "executeBonjourFunction") {
          // Execute the function associated with the button with id="bonjour"
          document.getElementById("bonjour").click();
        }
      }
    }),
      window.addEventListener("beforeinstallprompt", (event) => {
        // Prevent Chrome 67 and earlier from automatically showing the prompt
        event.preventDefault();

        // Stash the event so it can be triggered later.
        this.deferredPrompt = event;

        // Update UI to notify the user that the app can be installed
        // You can show a button or any other UI element to prompt the user to install the app.
        // For example, you can set a data property like showInstallButton to true and use v-if in your template.
      });
  },
  methods: {
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
};
</script>
