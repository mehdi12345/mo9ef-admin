<script>
import firebase from "firebase/app";
import "firebase/firestore";
/**
 * Transactions component
 */
export default {
  data() {
    return {
      annonce: [],
      filter: "",
      perPage: 15,
      currentPage: 1,
      buzy: false,
      fields: [
        { key: "fullname", label: "Nom du client" },
        { key: "email", label: "Address email" },
        { key: "phone", label: "Numero du telephone" },
      ],
    };
  },
  computed: {
    isBuzy: {
      get() {
        return this.buzy;
      },
      set(value) {
        this.buzy = value;
      },
    },

    // Total no. of records
    rows() {
      return this.annonce.length;
    },
  },
  mounted() {
    this.init();
    this.totalRows = this.annonce.length;
  },
  methods: {
    // Fetch the new ads from firebase
    async init() {
      this.isBuzy = true;
      this.annonce = [];
      var db = firebase.firestore();
      db.collection("users")
        .get()
        .then((user) => {
          user.forEach((doc) => {
            this.annonce.push({
              fullname: doc.data().fullname,
              email: doc.data().email,
              phone: doc.data().phone,
            });
          });
          this.isBuzy = false;
        });
    },
    /**
     * Search the table data with search input
     */
    onFiltered(filteredItems) {
      // Trigger pagination to update the number of buttons/pages due to filtering
      this.totalRows = filteredItems.length;
      this.currentPage = 1;
    },
  },
};
</script>

<template>
  <div class="card">
    <div class="card-body">
      <h4 class="card-title mb-4">Dernières clients</h4>
      <div class="row mt-4">
        <!-- Search -->
        <div class="col-sm-12 col-md-2">
          <div id="tickets-table_filter" class="dataTables_filter text-md-right">
            <label class="d-inline-flex align-items-center">
              Search:
              <b-form-input
                v-model="filter"
                type="search"
                class="form-control form-control-sm ml-2"
              ></b-form-input>
            </label>
          </div>
        </div>
        <!-- End search -->
      </div>
      <div class="table-responsive">
        <b-table
          :items="annonce"
          :fields="fields"
          responsive="sm"
          :per-page="perPage"
          :current-page="currentPage"
          :filter="filter"
          :busy.sync="isBuzy"
          @filtered="onFiltered"
          empty-text="Aucune client trouvée"
          show-empty
        >
          <template #empty="scope">
            <div class="text-center">
              <h6 class="align-middle">{{ scope.emptyText }}</h6>
            </div>
          </template>
          <template #table-busy>
            <div class="text-center text-primary my-2">
              <b-spinner class="align-middle"></b-spinner>
              <strong> Loading...</strong>
            </div>
          </template>
          <template v-slot:cell(paymentstatus)="row">
            <div
              class="badge font-size-12"
              :class="{
                'badge-soft-danger': `${row.value}` === 'Chargeback',
                'badge-soft-success': `${row.value}` === 'Paid',
                'badge-soft-warning': `${row.value}` === 'Unpaid',
              }"
            >
              {{ row.value }}
            </div>
          </template>
        </b-table>
      </div>
      <div class="row">
        <div class="col">
          <div class="dataTables_paginate paging_simple_numbers float-right">
            <ul class="pagination pagination-rounded mb-0">
              <!-- pagination -->
              <b-pagination
                v-model="currentPage"
                :total-rows="rows"
                :per-page="perPage"
              ></b-pagination>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
