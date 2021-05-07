<template>
  <div>
    <b-navbar variant="faded" type="light">
      <b-navbar-brand tag="h1" class="mb-0">
        Sally Lab LeetCode Challenge
      </b-navbar-brand>
      <b-icon
        v-b-tooltip.hover.html.right="tipMethod"
        icon="terminal"
        font-scale="1.5"
      ></b-icon>
    </b-navbar>
    <b-tabs content-class="mt-3" fill>
      <b-tab active>
        <template #title>
          <b-icon icon="cursor-fill" font-scale="1"></b-icon>
          <strong>Send Record</strong>
        </template>
        <the-form ref="form"></the-form>
      </b-tab>
      <b-tab>
        <template #title>
          <b-icon icon="trophy-fill" font-scale="1"></b-icon>
          <strong>Lab Record</strong>
        </template>
        <the-record></the-record>
      </b-tab>

      <b-tab>
        <template #title>
          <b-icon icon="gear" font-scale="1"></b-icon> <strong>Setting</strong>
        </template>
        <the-setting @refresh="refreshReminder"></the-setting>
      </b-tab>
    </b-tabs>
  </div>
</template>

<script>
import TheForm from "./components/TheForm.vue";
import TheRecord from "./components/TheRecord.vue";
import TheSetting from "./components/TheSetting.vue";

import Firebase from "firebase";
let config = {
  apiKey: "AIzaSyDSjBMCmRfRXoV1K-5sf6baklLPgcicNas",
  authDomain: "sallylab-leetcode.firebaseapp.com",
  projectId: "sallylab-leetcode",
  databaseURL: "https://sallylab-leetcode-default-rtdb.firebaseio.com",
  storageBucket: "sallylab-leetcode.appspot.com",
  messagingSenderId: "231272691085",
  appId: "1:231272691085:web:7b387c879af97195c2f9c4",
};
let app = Firebase.initializeApp(config);
let db = app.database();
export default {
  data() {
    return {
      progress: 0,
    };
  },
  provide: {
    ref: app.database().ref("record"),
    db: app.database(),
  },
  components: {
    TheForm,
    TheRecord,
    TheSetting,
  },
  methods: {
    tipMethod() {
      let tmp = 0;
      db.ref("/record/")
        .once("value")
        .then((result) => {
          for (const i in result.val()) {
            console.log(i);
            tmp += 1;
          }
          this.progress = tmp;
        });
      if (this.progress === 0) {
        return "<strong>We haven't solved </br>" + "any problem</strong>";
      } else if (this.progress === 1) {
        return (
          "<strong>We have solved </br>" +
          this.progress +
          "</br>problem</strong>"
        );
      } else {
        return (
          "<strong>We have solved </br>" +
          this.progress +
          "</br>problems</strong>"
        );
      }
    },
    refreshReminder() {
      this.$refs.form.setReminder();
    },
  },
};
</script>

<style>
</style>