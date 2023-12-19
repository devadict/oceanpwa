<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <a
      v-if="deferredPrompt"
      @click="installApp"
      id="bonjour"
      href="https://shiny-unicorn-80d9bd.netlify.app/#/"
    >
      ReInstall App
    </a>
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
    });
  },
  methods: {
    installApp(event) {
      // Prevent the default action of the link
      event.preventDefault();

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
