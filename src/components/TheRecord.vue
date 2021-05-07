<template>
  <div>
    <b-card>
      <b-form @submit.prevent="onSubmit">
        <b-form-group>
          <label>Problem Number</label>
          <b-form-input
            v-model="problem"
            type="number"
            :state="problemValidation"
          ></b-form-input>
        </b-form-group>

        <b-form-group>
          <label>Language</label>
          <b-form-input
            list="input-list-record"
            v-model="language"
            :state="languageValidation"
          ></b-form-input>
          <b-form-datalist
            id="input-list-record"
            :options="languages"
          ></b-form-datalist>
        </b-form-group>

        <b-button
          class="mr-1"
          type="submit"
          variant="primary"
        >Submit</b-button>
      </b-form>
    </b-card>

    <b-card v-if="!haveRecord && submitted" text-variant="danger">
      <b-card-text> <b-icon icon="x-circle" scale="1" variant="danger"></b-icon> No Record</b-card-text>
    </b-card>

    <b-card v-else-if="submitted">
      <b-table
        :items="records"
        :fields="fields"
        striped
      >
        <template #cell(show_details)="row">

          <b-button
            size="sm"
            @click="row.toggleDetails"
            class="mr-2"
          >
            <b-icon
              v-if="!row.detailsShowing"
              icon="trash-fill"
              aria-hidden="true"
            ></b-icon> {{ row.detailsShowing ? 'Close' : 'Delete'}}
          </b-button>

        </template>

        <template #row-details="row">
          <b-card>
            <b-card-text>
              Are You Sure ?
            </b-card-text>

            <b-button
              size="sm"
              @click="showRecords(row.item.name)"
            >
              Yes
            </b-button>
          </b-card>
        </template>
      </b-table>

      <b-button
        @click="deleteRecord"
        class="mb-2"
        variant="danger"
      >
        <b-icon
          icon="trash-fill"
          aria-hidden="true"
        ></b-icon> Delete This Problem
      </b-button>
    </b-card>

  </div>
</template>

<script>
export default {
  inject: ["ref", "db"],
  data() {
    return {
      haveRecord: false,
      problem: null,
      submitted: false,
      language: null,
      languages: ["All", "C++", "Python", "JavaScript"],
      fields: [
        {
          key: "name",
          sortable: false,
        },
        {
          key: "language",
          sortable: false,
        },
        {
          key: "runtime",
          label: "Runtime",
          sortable: true,
          // Variant applies to the whole column, including the header and footer
        },
        {
          key: "show_details",
          label: "Delete",
          sortable: false,
        },
      ],
      records: [],
    };
  },
  computed: {
    problemValidation() {
      if (this.problem != null && this.problem <= 0) {
        return false;
      } else {
        return null;
      }
    },
    languageValidation() {
      if (this.language != null && this.language.trim() === "") {
        return false;
      } else {
        return null;
      }
    },
  },
  methods: {
    onSubmit() {
      if (
        this.problem !== null &&
        this.language !== null &&
        this.problem > 0 &&
        this.language.trim() !== ""
      ) {
        this.records = [];

        this.db
          .ref("/record/" + this.problem)
          .once("value")
          .then((result) => {
            if (result.exists()) {
              // 那一題有人回答
              this.submitted = true;
              this.haveRecord = true;

              this.language = this.language.trim();
              this.language = this.language.charAt(0).toUpperCase() + this.language.slice(1);
              // 選擇All
              if (this.language === "All") {
                for (const name in result.val()) {
                  const record = {
                    name: name,
                    language: result.val()[name].language,
                    runtime: result.val()[name].runtime,
                    _showDetails: false,
                  };

                  this.records.push(record);
                }
              }
              // 選擇某一個語言
              else {
                for (const name in result.val()) {
                  if (result.val()[name].language === this.language) {
                    const record = {
                      name: name,
                      language: result.val()[name].language,
                      runtime: result.val()[name].runtime,
                      _showDetails: false,
                    };
                    this.records.push(record);
                  }
                }
              }
            } else {
              this.submitted = true;
              this.haveRecord = false;
            }
          });
      }
    },
    deleteRecord() {
      this.ref.child(this.problem).remove();
      this.haveRecord = false;
      this.submitted = false;
    },
    showRecords(name) {
      console.log("/record/" + this.problem + name);
      this.db
        .ref("/record/" + this.problem + "/" + name)
        .remove()
        .then(() => {
          this.onSubmit();
        });
    },
  },
  languageClick() {
    console.log("click");
  },
};
</script>