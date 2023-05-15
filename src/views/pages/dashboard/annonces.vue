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
      fields: [
        { key: "image", label: "Image" },
        { key: "title", label: "Titre" },
        { key: "city", label: "Ville" },
        { key: "price", label: "Prix" },
        { key: "client", label: "Client" },
        { key: "createdAt", label: "Date" },
        { key: "status", label: "Status" },
        { key: "action", label: "Action" },
      ],
    };
  },
  computed: {
    /**
     * Total no. of records
     */
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
      this.annonce = [];
      var db = firebase.firestore();
      db.collection("ads")
        .where("status", "==", 0)
        .get()
        .then((querySnapshot) => {
          querySnapshot.forEach((doc) => {
            db.collection("users")
              .doc(doc.data().client)
              .get()
              .then((user) => {
                this.annonce.push({
                  id: doc.id,
                  title: doc.data().title,
                  image: doc.data().images[0],
                  city: doc.data().city,
                  price: doc.data().price,
                  client: user.data().fullname,
                  createdAt: new Date(doc.data().createdAt).toLocaleDateString("fr-FR"),
                  status: doc.data().status,
                });
              });
          });
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

    approve(id) {
      var db = firebase.firestore();
      db.collection("ads")
        .doc(id)
        .update({
          status: 1,
        })
        .then(() => {
          this.init();
        });
    },

    decline(id) {
      var db = firebase.firestore();
      db.collection("ads")
        .doc(id)
        .update({
          status: -1,
        })
        .then(() => {
          this.init();
        });
    },
  },
};
</script>

<template>
  <div class="card">
    <div class="card-body">
      <h4 class="card-title mb-4">Annonces non acceptez</h4>
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
          @filtered="onFiltered"
          show-empty
          empty-text="Aucune nouveau annonce trouvée"
        >
          <template #empty="scope">
            <div class="text-center">
              <h6 class="align-middle">{{ scope.emptyText }}</h6>
            </div>
          </template>
          <template v-slot:cell(status)="row">
            <div
              v-if="row.item.status === 0"
              class="badge badge-soft-warning font-size-12"
            >
              En attente
            </div>
            <div
              v-else-if="row.item.status === 1"
              class="badge badge-soft-success font-size-12"
            >
              Accepté
            </div>
            <div v-else class="badge badge-soft-danger font-size-12">Refusé</div>
          </template>
          <template v-slot:cell(image)="data">
            <img
              :src="data.item.image"
              :alt="data.item.description"
              :srcset="data.item.image"
              height="80"
              width="80"
            />
          </template>

          <template v-slot:cell(action)="data">
            <b-button size="sm" variant="outline-success" @click="approve(data.item.id)">
              Approvez
            </b-button>
            <b-button size="sm" variant="outline-danger" @click="decline(data.item.id)">
              Supprimer
            </b-button>
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
