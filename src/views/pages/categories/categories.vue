<script>
import firebase from "firebase/app";
import "firebase/firestore";
import "firebase/storage";
/**
 * Transactions component
 */
export default {
  data() {
    return {
      annonce: [],
      filter: "",
      perPage: 15,
      buzy: false,
      currentPage: 1,
      fields: [
        { key: "uid", label: "UID" },
        { key: "image", label: "Image" },
        { key: "title", label: "title" },
        { key: "action", label: "Action" },
      ],
      title: "",
      image: undefined,
      modal: false,
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
    /**
     * Total no. of records
     */
    rows() {
      return this.annonce.length;
    },
    authUser() {
      return this.$store.state.auth.currentUser;
    },
    openModal: {
      get() {
        return this.modal;
      },
      set(value) {
        this.modal = value;
      },
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
      db.collection("machine_categories")
        .get()
        .then((querySnapshot) => {
          querySnapshot.forEach((doc) => {
            this.annonce.push({
              uid: doc.id,
              title: doc.data().name,
              image: doc.data().image,
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
      console.log(this.title);
      // upload image to firebase
      var newThis = this;
      var storageRef = firebase.storage().ref();
      var file = this.image;
      var imagename = file.name;
      var metadata = { contentType: file.type };
      var uploadTask = storageRef.child("categories/" + imagename).put(file, metadata);
      uploadTask.on(
        "state_changed",
        function (snapshot) {
          // Observe state change events such as progress, pause, and resume
          // Get task progress, including the number of bytes uploaded and the total number of bytes to be uploaded
          var progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
          console.log("Upload is " + progress + "% done");
          switch (snapshot.state) {
            case firebase.storage.TaskState.PAUSED: // or 'paused'
              console.log("Upload is paused");
              break;
            case firebase.storage.TaskState.RUNNING: // or 'running'
              console.log("Upload is running");
              break;
          }
        },
        function (error) {
          // Handle unsuccessful uploads
          console.log(error);
        },
        function () {
          // Handle successful uploads on complete
          // For instance, get the download URL: https://firebasestorage.googleapis.com/...
          uploadTask.snapshot.ref.getDownloadURL().then(function (downloadURL) {
            console.log("File available at", downloadURL);
            var db = firebase.firestore();
            db.collection("machine_categories")
              .add({
                name: newThis.title,
                image: downloadURL,
              })
              .then(function (docRef) {
                console.log("Document written with ID: ", docRef.id);
                newThis.init();
                newThis.title = "";
                newThis.image = undefined;
                newThis.openModal = false;
              })
              .catch(function (error) {
                console.error("Error adding document: ", error);
              });
          });
        }
      );
    },
    supprimer(item) {
      //delete image from firebase with url
      this.$bvModal
        .msgBoxConfirm(
          "Voulez vous vraiment supprimer cet category? tout les annonce de ce category en va supprimer",
          {
            okTitle: "Ok",
            cancelTitle: "Annuler",
            okVariant: "danger",
            cancelVariant: "secondary",
          }
        )
        .then((data) => {
          if (data) {
            var storageRef = firebase.storage().refFromURL(item.image);
            storageRef
              .delete()
              .then(function () {
                console.log("Image deleted successfully");
              })
              .catch(function (error) {
                console.log("Error deleting image: ", error);
              });
            //delete item from firebase
            var db = firebase.firestore();
            var newthis = this;
            // delete les annonce de cette category
            db.collection("annonces")
              .where("category", "==", item.uid)
              .get()
              .then(function (querySnapshot) {
                querySnapshot.forEach(function (doc) {
                  db.collection("annonces")
                    .doc(doc.id)
                    .delete()
                    .then(function () {
                      console.log("Document successfully deleted!");
                    })
                    .catch(function (error) {
                      console.error("Error removing document: ", error);
                    });
                });
              });
            // wait for annonce to be deleted
            setTimeout(function () {
              db.collection("machine_categories")
                .doc(item.uid)
                .delete()
                .then(function () {
                  console.log("Document successfully deleted!");
                  newthis.init();
                })
                .catch(function (error) {
                  console.error("Error removing document: ", error);
                });
            }, 2000);
          }
        });
    },
  },
};
</script>

<template>
  <div class="card">
    <!-- register modal -->
    <b-modal v-model="openModal" id="ajouter-modal" title="Nouveau category">
      <form>
        <div class="form-group">
          <label for="exampleInputnom1">Nom complet</label>
          <input
            type="text"
            v-model="title"
            class="form-control"
            id="exampleInputnom1"
            aria-describedby="emailHelp"
            placeholder="Nom complet"
          />
        </div>
        <div class="form-group">
          <label for="exampleInputEmail1">Adresse email</label>
          <b-form-file
            v-model="image"
            :state="Boolean(image)"
            placeholder="Choisir une image"
            drop-placeholder="Déposer une image"
          ></b-form-file>
        </div>
      </form>

      <template #modal-footer>
        <button type="button" class="btn btn-secondary" @click="openModal = false">
          Fermer
        </button>
        <button type="button" class="btn btn-primary" @click="ajouter">
          Enregistrer
        </button>
      </template>
    </b-modal>
    <!-- end register modal -->
    <div class="card-body">
      <h4 class="card-title mb-4">Dernières annonces</h4>
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
          <b-button @click="openModal = true" class="btn btn-success align-items-center">
            Ajoutez une category
          </b-button></b-col
        >
        <!-- Search -->

        <!-- End search -->
      </b-row>
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
          show-empty
          empty-text="Accune catégorie trouvée"
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
          <template v-slot:cell(image)="row">
            <div>{{ row.image }}</div>
            <img
              :src="row.item.image"
              :alt="row.item.title"
              :srcset="row.item.image"
              height="100"
              width="100"
            />
          </template>

          <template v-slot:cell(action)="row">
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
