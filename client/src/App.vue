<template>
  <v-app>
    <v-main>
      <v-app-bar color="teal" fixed app dark>
        <v-toolbar-title>Vue Socket.IO Chat</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-title>Users Online: {{ users.length }}</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-title>User: {{ username }}</v-toolbar-title>
      </v-app-bar>

      <v-container class="textContainer">
        <v-sheet
          v-for="(msg, index) in messages"
          :key="index"
          class="mx-auto my-6"
          max-width="1000"
          outlined
        >
          <v-list-item three-line>
            <v-list-item-content>
              <v-list-item-subtitle>{{msg.username}}</v-list-item-subtitle>
              {{msg.msg}}
            </v-list-item-content>
          </v-list-item>
        </v-sheet>
        <div class="bottom"></div>
      </v-container>
      <div class="text-center">
        <v-dialog v-model="dialog" width="500" persistent>
          <v-card>
            <v-card-title>Add Username</v-card-title>
            <ValidationObserver ref="observer">
              <form>
                <ValidationProvider v-slot="{ errors }" name="Name" rules="required">
                  <v-text-field
                    class="mx-2 mt-4"
                    v-on:keyup.enter="sendMessage(msg)"
                    rounded
                    outlined
                    color="teal"
                    v-model="username"
                    label="Username"
                    :error-messages="errors"
                    required
                  ></v-text-field>
                </ValidationProvider>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn @click="submit" dark fab color="teal">
                    <v-icon>mdi-account-arrow-right</v-icon>
                  </v-btn>
                </v-card-actions>
              </form>
            </ValidationObserver>
          </v-card>
        </v-dialog>
      </div>
      <v-footer color="white" fixed app>
        <v-text-field
          v-on:keyup.enter="sendMessage(msg)"
          rounded
          outlined
          color="teal"
          v-model="msg"
          label="Message"
        ></v-text-field>
        <v-btn @click="sendMessage(msg)" fab dark color="teal" class="mb-8 ml-4">
          <v-icon>mdi-send</v-icon>
        </v-btn>
      </v-footer>
    </v-main>
  </v-app>
</template>

<script>
import { required } from "vee-validate/dist/rules";
import { extend, ValidationObserver, ValidationProvider } from "vee-validate";

import io from "socket.io-client";

extend("required", {
  ...required,
  message: "{_field_} can not be empty",
});

export default {
  name: "app",
  data: function () {
    return {
      msg: "",
      username: "",
      socket: io("http://localhost:1337"),
      messages: [],
      users: [],
      dialog: true,
      showChat: false,
    };
  },
  components: {
    ValidationProvider,
    ValidationObserver,
  },
  methods: {
    joinServer: function () {
      this.socket.on("loggedIn", (data) => {
        this.messages = data.messages;
        this.users = data.users;
      });
      this.listen();
    },
    listen: function () {
      this.socket.on("userOnline", (user) => {
        this.users.push(user);
      });
      this.socket.on("userLeft", (user) => {
        this.users.splice(this.users.indexOf(user), 1);
      });
      this.socket.on("msg", (message) => {
        this.messages.push(message);
        this.scrollToElement();
      });
    },
    sendMessage: function (message) {
      if (message != "") {
        this.socket.emit("msg", message);
        this.msg = "";
      }
    },
    scrollToElement: function () {
      const el = this.$el.getElementsByClassName("bottom")[0];

      if (el) {
        el.scrollIntoView();
      }
    },
    submit() {
      this.$refs.observer.validate().then((data) => {
        if (data) {
          this.dialog = false;
          this.socket.emit("newUser", this.username);
          this.showChat = true;
          this.scrollToElement();
        }
      });
    },
  },
  mounted: function () {
    this.joinServer();
  },
};
</script>

<style lang="scss">
.textContainer {
  height: auto !important;
  padding-bottom: 100px !important;
}
</style>