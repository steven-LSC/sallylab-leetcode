<template>
  <b-card>
    <b-form-group>
      <label>Reminder</label>
      <b-form-input
        :state="reminderValidation"
        v-model.trim="reminder"
      ></b-form-input>
      <b-form-invalid-feedback :state="reminderValidation">Can't be empty.</b-form-invalid-feedback>
      <b-form-valid-feedback :state="reminderValidation">Looks Good.</b-form-valid-feedback>
    </b-form-group>
    <b-button
      type="submit"
      variant="primary"
      @click="onSubmit"
    >Submit</b-button>
    <b-modal v-model="modalShow" ok-only title="Info">Success!</b-modal>
  </b-card>
</template>

<script>
export default {
  inject: ["ref", "db"],
  data() {
    return {
      reminder: "",
      submitted: false,
      modalShow: false,
    };
  },
  computed: {
    reminderValidation() {
      if (this.reminder === "" && this.submitted === true) {
        return false;
      } else {
        return null;
      }
    },
  },
  methods: {
    onSubmit() {
      if (this.reminder !== "") {
        this.ref.child("reminder").set({ context: this.reminder });
        this.$emit("refresh");
        this.modalShow = true;
      } else {
        this.submitted = true;
      }
    },
  },
  mounted: function () {
    this.db
      .ref("/record/reminder")
      .once("value")
      .then((result) => {
        for (const name in result.val()) {
          this.reminder = result.val()[name];
        }
      });
  },
};
</script>