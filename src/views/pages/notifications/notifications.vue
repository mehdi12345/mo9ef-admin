<script>
import firebase from "firebase/app";
import "firebase/firestore";
import { required } from "vuelidate/lib/validators";

/**
 * Transactions component
 */
export default {
  data() {
    return {
      notifications: [],
      filter: "",
      perPage: 15,
      currentPage: 1,
      buzy: false,
      modal: false,
      editmodal: false,
      fields: [
        { key: "uid" },
        { key: "title", label: "Titre" },
        { key: "body", label: "Body" },
        { key: "action", label: "Action" },
      ],

      title: "",
      body: "",
      uid: "",
    };
  },
  // validation
  validations: {
    title: {
      required,
    },
    body: {
      required,
    },
  },

  computed: {
    authUser() {
      return this.$store.state.auth.currentUser;
    },
    openModal: {
      get() {
        return this.modal;
      },
      set(value) {
        this.clear();
        this.modal = value;
      },
    },
    openEditModal: {
      get() {
        return this.editmodal;
      },
      set(value) {
        this.editmodal = value;
      },
    },
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
      return this.notifications.length;
    },
  },
  mounted() {
    this.init();
    this.totalRows = this.notifications.length;
  },
  methods: {
    // Fetch the new ads from firebase
    async init() {
      this.isBuzy = true;
      this.notifications = [];
      var db = firebase.firestore();
      db.collection("notifications")
        .get()
        .then((user) => {
          user.forEach((doc) => {
            this.notifications.push({
              uid: doc.id,
              title: doc.data().title,
              body: doc.data().body,
              date: new Date(doc.data().date).toLocaleDateString("fr-FR"),
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
    ajouter() {
      // validate
      this.$v.$touch();
      if (this.$v.$invalid) return;
      else {
        this.isBuzy = true;
        var db = firebase.firestore();

        db.collection("notifications")
          .add({
            title: this.title,
            body: this.body,
          })
          .then(() => {
            this.isBuzy = false;
            this.modal = false;
            this.clear();
            this.init();
          })
          .catch((error) => {
            this.isBuzy = false;
            console.error("Error adding document: ", error);
          });
      }
    },
    modifierNotification(notif) {
      this.uid = notif.uid;
      this.title = notif.title;
      this.body = notif.body;
      this.openEditModal = true;
    },
    modifier() {
      // validate
      this.$v.$touch();
      if (this.$v.$invalid) return;
      else {
        this.isBuzy = true;
        var db = firebase.firestore();
        db.collection("notifications")
          .doc(this.uid)
          .update({
            uid: this.uid,
            title: this.title,
            body: this.body,
          })
          .then(() => {
            this.isBuzy = false;
            this.editmodal = false;
            this.clear();
            this.init();
          })
          .catch((error) => {
            this.isBuzy = false;
            console.error("Error adding document: ", error);
          });
      }
    },
    clear() {
      this.title = "";
      this.body = "";
      this.uid = "";
    },
    supprimer(notif) {
      //ckeck if user is going to  delete
      this.$bvModal
        .msgBoxConfirm("Voulez vous vraiment supprimer cet notification?", {
          okTitle: "Ok",
          cancelTitle: "Annuler",
          okVariant: "danger",
          cancelVariant: "secondary",
        })
        .then((data) => {
          if (data) {
            var db = firebase.firestore();
            db.collection("notifications")
              .doc(notif.uid)
              .delete()
              .then(() => {
                this.isBuzy = false;
                this.modal = false;
                this.clear();
                this.init();
              })
              .catch((error) => {
                this.isBuzy = false;
                console.error("Error removing document: ", error);
              });
          }
        });
    },
    send(item) {
      console.log(item);
    },
  },
};
</script>

<template>
  <div class="card">
    <!-- register modal -->
    <b-modal v-model="openModal" id="ajouter-modal" title="Nouveau notification">
      <form @submit.prevent="ajouter">
        <div class="form-group">
          <label for="exampleInputnom1">Titre du notification</label>
          <input
            type="text"
            v-model="title"
            class="form-control"
            id="exampleInputnom1"
            aria-describedby="emailHelp"
            placeholder="Titre"
          />
          <div class="error" v-if="!$v.title.required">le titre est obligatoire</div>
        </div>
        <div class="form-group">
          <label for="exampleInputEmail1">Message du notification</label>
          <textarea
            type="text"
            v-model="body"
            class="form-control"
            id="exampleInputEmail1"
            aria-describedby="emailHelp"
            placeholder="Message"
          />
        </div>
        <div class="error" v-if="!$v.body.required">Message est obligatoire</div>
      </form>

      <template #modal-footer>
        <button
          type="button"
          class="btn btn-secondary"
          @click="
            openModal = false;
            clear();
          "
        >
          Fermer
        </button>
        <button type="button" @click="ajouter()" class="btn btn-primary">
          Enregistrer
        </button>
      </template>
    </b-modal>
    <!-- end register modal -->
    <!--- edit modal -->
    <b-modal v-model="openEditModal" id="modifer-modal" title="Modifier notifcation">
      <form @submit.prevent="ajouter">
        <div class="form-group">
          <label for="exampleInputnom1">Titre du notification</label>
          <input
            type="text"
            v-model="title"
            class="form-control"
            id="exampleInputnom1"
            aria-describedby="emailHelp"
            placeholder="Titre"
          />
          <div class="error" v-if="!$v.title.required">le Titre est obligatoire</div>
        </div>
        <div class="form-group">
          <label for="exampleInputEmail1">Message</label>
          <textarea
            v-model="body"
            class="form-control"
            id="exampleInputEmail1"
            aria-describedby="emailHelp"
            placeholder="Message"
          />
        </div>
        <div class="error" v-if="!$v.body.required">Message est obligatoire</div>
      </form>

      <template #modal-footer>
        <button type="button" class="btn btn-secondary" @click="openEditModal = false">
          Fermer
        </button>
        <button type="button" @click="modifier()" class="btn btn-primary">
          Enregistrer
        </button>
      </template>
    </b-modal>
    <!-- end edit modal -->
    <div class="card-body">
      <h4 class="card-title mb-4">Notifications</h4>
      <b-row align-h="between" class="mb-2">
        <b-col cols="2"
          ><div id="tickets-table_filter" class="dataTables_filter text-md-right">
            <label class="d-inline-flex align-items-center">
              Search:
              <b-form-input
                v-model="filter"
                type="search"
                class="form-control form-control-sm ml-2"
              ></b-form-input>
            </label></div
        ></b-col>
        <b-col cols="2">
          <b-button v-b-modal.ajouter-modal class="btn btn-success align-items-center">
            Ajoutez un nouveau notification
          </b-button></b-col
        >
        <!-- Search -->

        <!-- End search -->
      </b-row>
      <div class="table-responsive">
        <b-table
          :items="notifications"
          :fields="fields"
          responsive="sm"
          :per-page="perPage"
          :current-page="currentPage"
          :filter="filter"
          :busy.sync="isBuzy"
          show-empty
          @filtered="onFiltered"
          empty-text="Aucune notification trouvée"
          empty-filtered-text="Aucune notification trouvée"
        >
          <template #table-busy>
            <div class="text-center text-primary my-2">
              <b-spinner class="align-middle"></b-spinner>
              <strong> Loading...</strong>
            </div>
          </template>
          <template #empty="scope">
            <div class="text-center">
              <h6 class="align-middle">{{ scope.emptyText }}</h6>
            </div>
          </template>

          <template v-slot:cell(action)="row">
            <b-button
              size="sm"
              variant="outline-success"
              @click="send(row.item)"
              class="mr-2"
            >
              Envoyer
            </b-button>
            <b-button
              size="sm"
              variant="outline-primary"
              @click="
                openEditModal = true;
                modifierNotification(row.item);
              "
              class="mr-2"
            >
              Modifier
            </b-button>
            <b-button size="sm" variant="outline-danger" @click="supprimer(row.item)">
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
