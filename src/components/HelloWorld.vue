<template>
<div class="hello">
  <button @click="sendTargetPicker">Send Sample</button>
</div>
</template>

<script>
import {
  onMounted
} from "vue";
import liff from "@line/liff";
export default {
  name: "HelloWorld",
  setup() {
    onMounted(async () => {
      try {
        await liff.init({
          liffId: process.env.VUE_APP_SHARE_ID,
        });
        if (!liff.isLoggedIn())
          liff.login({
            redirectUri: window.location.href,
          });
      } catch (err) {
        console.log(`liff.state init error ${err}`);
      }
    });
    async function sendTargetPicker() {
      if (!liff.isLoggedIn()) {
        liff.login({
          redirectUri: window.location.href,
        });
      }
      if (liff.isApiAvailable("shareTargetPicker")) {
        try {
          const picker = await liff.shareTargetPicker([{
            type: "text",
            text: "Hello, World!",
          }, ]);
          if (picker) {
            // succeeded in sending a message through TargetPicker
            console.log(`[${picker.status}] Message sent!`);
          } else {
            const [majorVer, minorVer] = (liff.getLineVersion() || "").split(
              "."
            );
            if (parseInt(majorVer) == 10 && parseInt(minorVer) < 11) {
              console.log(
                "TargetPicker was opened at least. Whether succeeded to send message is unclear"
              );
            } else console.log("TargetPicker was closed!");
          }
        } catch (error) {
          // something went wrong before sending a message
          console.log(error);
          console.log("Flex Message got some error");
          liff.closeWindow();
        }
      } else console.log("Please login...");
    }
    return {
      sendTargetPicker,
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
