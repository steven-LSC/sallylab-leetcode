<template>
  <b-card>
    <b-alert
      v-if="reminder"
      variant="danger"
      show
    >
      <b-icon icon="info-circle-fill" scale="1" variant="black"></b-icon> {{reminder}}
    </b-alert>
    <b-form @submit.prevent="onSubmit">

      <b-form-group>
        <label>Problem Number</label>
        <b-form-input
          v-model="problemNum"
          :state="problemNumValidation"
          type="number"
        ></b-form-input>
        <b-form-invalid-feedback :state="problemNumValidation">Problem number invalid.</b-form-invalid-feedback>
        <b-form-valid-feedback :state="problemNumValidation">Looks Good.</b-form-valid-feedback>
      </b-form-group>

      <b-form-group>
        <label>Your Name</label>
        <b-form-input
          v-model="name"
          :state="nameValidation"
        ></b-form-input>
        <b-form-invalid-feedback :state="nameValidation">Your name can't be empty.</b-form-invalid-feedback>
        <b-form-valid-feedback :state="nameValidation">Looks Good.</b-form-valid-feedback>
      </b-form-group>

      <b-form-group>
        <label>Language</label>
        <b-form-input
          list="input-list"
          id="input-with-list"
          v-model="language"
          :state="languageValidation"
        ></b-form-input>
        <b-form-datalist
          id="input-list"
          :options="languages"
        ></b-form-datalist>
        <b-form-invalid-feedback :state="languageValidation">Language can't be empty.</b-form-invalid-feedback>
        <b-form-valid-feedback :state="languageValidation">Looks Good.</b-form-valid-feedback>
      </b-form-group>

      <b-form-group>
        <label>Runtime</label>
        <b-form-input
          v-model="runtime"
          :state="runtimeValidation"
          type="number"
          placeholder="ms"
        ></b-form-input>
        <b-form-invalid-feedback :state="runtimeValidation">Runtime number invalid.</b-form-invalid-feedback>
        <b-form-valid-feedback :state="runtimeValidation">Looks Good.</b-form-valid-feedback>
      </b-form-group>

      <b-button
        type="submit"
        variant="primary"
      >Submit</b-button>
    </b-form>
    <b-modal
      v-model="submitted"
      id="modal-1"
      title="Confirmation"
      @ok="handleOk"
      ok-only
    >
      <p>Data was submitted successfully</p>
    </b-modal>

  </b-card>
</template>

<script>
export default {
  inject: ["ref", "db"],
  data() {
    return {
      problemNum: null,
      name: null,
      runtime: null,
      language: null,
      languages: ["C++", "Python", "JavaScript"],
      submitted: false,
      reminder: "",
    };
  },
  computed: {
    problemNumValidation() {
      if (this.problemNum != null && this.problemNum <= 0) {
        return false;
      } else {
        return null;
      }
    },
    nameValidation() {
      if (this.name !== null && this.name.trim() === "") {
        return false;
      } else {
        return null;
      }
    },
    languageValidation() {
      if (this.language !== null && this.language.trim() === "") {
        return false;
      } else {
        return null;
      }
    },
    runtimeValidation() {
      if (this.runtime !== null && this.runtime <= 0) {
        return false;
      } else {
        return null;
      }
    },
  },
  methods: {
    onSubmit() {
      //Submit前自己檢查就好 有問題本來就會顯示
      if (
        this.problemNum > 0 &&
        this.name.trim() !== "" &&
        this.language.trim() !== "" &&
        this.runtime > 0
      ) {
        this.language = this.language.trim();
        this.language =
          this.language.charAt(0).toUpperCase() + this.language.slice(1);
        this.ref
          .child(this.problemNum)
          .child(this.name)
          .set({ language: this.language, runtime: parseInt(this.runtime) });
        this.submitted = true;
        this.problemNum = null;
        this.name = null;
        this.runtime = null;
        this.language = null;
      }
    },
    handleOk() {
      this.submitted = false;
    },
    setReminder() {
      this.db
        .ref("/record/reminder")
        .once("value")
        .then((result) => {
          for (const name in result.val()) {
            this.reminder = result.val()[name];
          }
          console.log(this.reminder);
        });
    },
  },
  mounted: function () {
    this.setReminder();
  },
};
</script>